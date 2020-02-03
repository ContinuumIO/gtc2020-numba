FROM nvcr.io/nvidia/tensorflow:18.05-py3


USER root

################## DONT CHANGE BELOW ########################3
# Common DLI installs/config
# Install nginx version with extras
RUN apt-get update && apt-get install -y wget && wget -qO - https://openresty.org/package/pubkey.gpg | apt-key add - && apt-get -y install software-properties-common && add-apt-repository -y "deb http://openresty.org/package/ubuntu $(lsb_release -sc) main" && apt-get update && apt-get install -y --no-install-recommends openresty supervisor curl wget git && rm -rf /var/lib/apt/lists/*
#add-apt-repository -y ppa:nginx/stable && apt-get -y update &&  apt-get install -y  --no-install-recommends nginx supervisor curl wget git && rm -rf /var/lib/apt/lists/*

RUN mkdir /dli
WORKDIR /dli

# DIGITS env vars, not used everywhere, but keep them here as common globals anyways
ENV DIGITS_URL_PREFIX=/digits
ENV DIGITS_JOBS_DIR=/dli/data/digits
ENV DIGITS_LOGFILE_FILENAME=$DIGITS_JOBS_DIR/digits.log

# Set startup
ENTRYPOINT $WORKDIR/startup_script.sh
################## DONT CHANGE ABOVE ########################3

################## BASE SERVICES BELOW, CHANGE WITH CAUTION ########################3
# Install assessment harness services & Jupyter
RUN apt-get update && apt-get install --upgrade -y --no-install-recommends \
    python3-venv \
    redis-server && \
    python3 -m venv /usr/local/assessment && \
    /usr/local/assessment/bin/pip install --upgrade pip && \
    /usr/local/assessment/bin/pip install celery flask redis jupyter

# Install Jupyter, etc.
RUN python3  -m pip install -U pip==8.0.1 && pip install -U pip
RUN pip install --ignore-installed ipython jupyter
################## BASE SERVICES ABOVE, CHANGE WITH CAUTION ########################3


################## TASK SPECIFIC BELOW, CHANGE AS NEEDED ########################3
# Install task specific stuff

# FIXME: Workaround non-existent entrypoint above
ENTRYPOINT

# Add user
RUN useradd -ms /bin/bash appuser
USER appuser
WORKDIR /home/appuser

# Download and install miniconda
RUN wget https://repo.anaconda.com/miniconda/Miniconda3-4.7.12.1-Linux-x86_64.sh
RUN bash Miniconda3-4.7.12.1-Linux-x86_64.sh -b -p /home/appuser/Miniconda3
ENV PATH=/home/appuser/Miniconda3/bin:$PATH

# Install Jupyter Notebook
RUN conda install -y jupyter notebook

# Install Numba and CUDA toolkit
ARG NUMBA_VERSION=0.48
ARG CONDA_CUDATOOLKIT=10.0
RUN conda install -y -c numba cudatoolkit=$CONDA_CUDATOOLKIT numba=$NUMBA_VERSION cupy
# Latest CuPy not in Anaconda yet
#RUN /home/appuser/Miniconda3/bin/pip install cupy-cuda10

RUN conda install -y scipy matplotlib pandas

RUN git clone https://github.com/ContinuumIO/gtc2020-numba

WORKDIR ./gtc2020-numba

ARG BRANCH="master"
RUN git fetch && git checkout $BRANCH

CMD /home/appuser/Miniconda3/bin/jupyter notebook --port 9999 --ip 0.0.0.0

################## TASK SPECIFIC ABOVE, CHANGE AS NEEDED ########################3
