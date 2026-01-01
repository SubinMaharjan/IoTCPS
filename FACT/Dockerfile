FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive
ENV FACT_HOME=/opt/FACT_core
ENV DATA_DIR=/media/data
ENV TZ=Etc/UTC

# ------------------------------------------------------------
# 1. System update & base dependencies
# ------------------------------------------------------------
RUN apt update && apt upgrade -y && \
    apt install -y \
    git \
    python3 \
    python3-venv \
    python3-pip \
    sudo \
    curl \
    wget \
    ca-certificates \
    lsb-release \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

RUN apt update && \
    apt install -y tzdata && \
    ln -fs /usr/share/zoneinfo/Etc/UTC /etc/localtime && \
    dpkg-reconfigure --frontend noninteractive tzdata

# Setting docker configuration
RUN mkdir -p /root/.docker
RUN cat > /root/.docker/config.json <<EOF
{
  "auths": {
    "https://index.docker.io/v1/": {}
  }
}
EOF

RUN useradd -m -s /bin/bash fact && \
    echo "fact ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

USER fact
WORKDIR /home/fact

# ------------------------------------------------------------
# 2. Clone FACT core
# ------------------------------------------------------------
RUN git clone https://github.com/fkie-cad/FACT_core.git
WORKDIR /home/fact/FACT_core

# ------------------------------------------------------------
# 3. Run pre-install script (requires sudo)
# ------------------------------------------------------------
RUN chmod +x src/install/pre_install.sh && \
    sudo src/install/pre_install.sh

# ------------------------------------------------------------
# 4. Create FACT data directory
# ------------------------------------------------------------
RUN sudo mkdir -p ${DATA_DIR} && \
    sudo chown -R fact:fact ${DATA_DIR}

# ------------------------------------------------------------
# 5. Necessary changes required for linking different dockers
# ------------------------------------------------------------
RUN sudo sed -i "s/localhost:{config.frontend.hasura.port}/hasura-graphql-engine-1:8080/g" src/storage/graphql/hasura/init_hasura.py
RUN sudo sed -i "s|f'{user}:{pw}@'|f'{user}:{pw}@hasura-postgres-1:5432'|g" src/storage/graphql/hasura/init_hasura.py
# RUN sudo sed -i 's|server = "localhost"|server = "hasura-postgres-1"|g' src/config/fact-core-config.toml
# RUN sudo sed -i "s|return {'from_env': 'FACT_DB_URL'}|return f'postgresql://{config.common.postgres.ro_user}:{config.common.postgres.ro_pw}@hasura-postgres-1:5432/fact_db'|g" src/storage/graphql/hasura/init_hasura.py
RUN sudo sed -i "s|run_cmd_with_logging('docker pull fkiecad/cwe_checker:stable')|run_cmd_with_logging('docker pull --platform=linux/amd64 fkiecad/cwe_checker:stable')|g" src/plugins/analysis/cwe_checker/install.py
# RUN sudo sed -i "s|from pathlib import Path|from pathlib import Path\nimport os\nSRC_DIR = Path(__file__).absolute().parent.parent.parent.parent\nsys.path.append(str(SRC_DIR))\nfrom helperFunctions.install import run_cmd_with_logging\ndocker_id = os.environ.get('DOCKER_ID', raise_error=False)\nrun_cmd_with_logging(f'docker network connect hasura_default {docker_id}')\n|g" src/storage/graphql/hasura/init_hasura.py



# ------------------------------------------------------------
# 6. Add firmadyne submodule and fix broken install script
# ------------------------------------------------------------
RUN git submodule add https://github.com/fkie-cad/FACT_firmadyne_analysis_plugin.git src/plugins/analysis/firmadyne
# Fix broken link
RUN pip3 install git+https://github.com/njv299/common_helper_process.git
RUN sudo sed -i "s|def __init__(self, plugin_administrator|def __init__(self, plugin_administrator=None|g" src/plugins/analysis/firmadyne/code/firmadyne.py
RUN sudo sed -i "s|super().__init__(plugin_administrator, config=config, timeout=timeout, no_multithread=True, recursive=recursive, plugin_path=__file__)|super().__init__(no_multithread=True, view_updater=plugin_administrator)|g" src/plugins/analysis/firmadyne/code/firmadyne.py
RUN sudo sed -i "s|VERSION = '0.4'|VERSION = '0.4'\n    FILE = __file__\n    RECURSIVE = True\n    CONFIG = None\n    TIMEOUT=600|g" src/plugins/analysis/firmadyne/code/firmadyne.py

# ------------------------------------------------------------
# 7. Setup empty docker config to avoid docker authentication issues
# ------------------------------------------------------------
# RUN mkdir -p $HOME/.docker && \
#     echo '{"credsStore":""}' > $HOME/.docker/config.json

# ------------------------------------------------------------
# 8. Create a symbolic link for python
# ------------------------------------------------------------
RUN sudo ln -s /usr/bin/python3 /usr/bin/python

# ------------------------------------------------------------
# 9. Install redis serve... This is missing in installation script
# ------------------------------------------------------------
RUN sudo apt install -y redis-server && \
    redis-server --daemonize yes

# ------------------------------------------------------------
# 10. Changes in installation script
# ------------------------------------------------------------

RUN sudo sed -i "/if __name__ == '__main__':/,+1c\
if __name__ == '__main__':\
\n    try:\
\n        install()\
\n    except Exception as e:\
\n        print('Trying fallback method...')\
\n        from pathlib import Path\
\n        from helperFunctions.install import run_cmd_with_logging\
\n        SRC_DIR = Path(__file__).resolve().parents[3]\
\n        run_cmd_with_logging(\"\"\"sed -i 's|server = \\\"localhost\\\"|server = \\\"hasura-postgres-1\\\"|g' src/config/fact-core-config.toml\"\"\", raise_error=False)\
\n        run_cmd_with_logging(\"redis-server --daemonize yes\", raise_error=False)\
\n        run_cmd_with_logging(\"pip3 install git+https://github.com/njv299/common_helper_process.git\", raise_error=False)\
\n        docker_id = os.environ.get('DOCKER_ID', None)\
\n        if not docker_id:\
\n            print('No DOCKER_ID environment variable set, cannot connect to hasura network.')\
\n            sys.exit(1)\
\n        run_cmd_with_logging(f'docker network connect hasura_default {docker_id}')\
\n        install()" src/install.py

# ------------------------------------------------------------
# Changes to make it run on the localhost
# ------------------------------------------------------------
RUN sudo sed -i \
  -e 's|http-socket = 127.0.0.1:5000|http-socket = 0.0.0.0:5000|' \
  -e 's|stats = 127.0.0.1:9191|stats = 0.0.0.0:9191|' \
  src/config/uwsgi_config.ini

# ------------------------------------------------------------
# 11. Add CVE lookup plugin if necessary.
# ------------------------------------------------------------
# RUN git submodule add \
#     https://github.com/fkie-cad/FACT_analysis-plugin_CVE-lookup.git \
#     src/plugins/analysis/cve-lookup && \
#     python3 src/install.py -B

# ------------------------------------------------------------
# 12. Expose FACT web interface
# ------------------------------------------------------------
EXPOSE 5000

CMD ["/bin/bash"]

# ------------------------------------------------------------
# 13. Next step: Start FACT core components
# ------------------------------------------------------------
#  a. Start fact container
    # docker run -it \
    # --name fact-core \
    # -p 5001:5000 \
    # -v /var/run/docker.sock:/var/run/docker.sock \
    # -v /Users/subinmaharjan/subin/dockerfiles/DockerData:/home/fact/data \
    # --user root \
    # fact /bin/bash
#  b. Connect to hasura network from inside the container
    # export DOCKER_ID=$(hostname)
    # echo $DOCKER_ID //check if docker_id is set properly
#  c. Once inside the container run:
#    src/install.py
#   ./start_all_installed_fact_components
# Note: If password for postgres is needed, it is 'postgrespassword'
# Open fact web interface at: http://127.0.0.1:5001


