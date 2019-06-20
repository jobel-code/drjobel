# -*- mode: ruby -*-
# vi: set ft=ruby :

# docker build -t amasing-drjobel-datascience .
# SAVE THE IMAGE
# docker save amasing-drjobel-datascience > amasing-drjobel-datascience.tar
# EXPORT CONTAINER
# docker export amasing-drjobel > amasing-drjobel.tar


# FROM jupyter/datascience-notebook
FROM amasing-drjobel:latest

USER root

RUN apt-get update && apt-get upgrade -y


RUN conda update -n base conda && conda upgrade --all

# Install Python3 packages that aren't available or up-to-date in Conda.
RUN  pip install --quiet -U --upgrade-strategy only-if-needed --no-cache-dir \
                bs4 \
                ggplot \
                lime \
                s2sphere \
                jxmlease \
                faker \
                earthpy \
                python-arango \
                google-cloud-core \
                google-api-core \
                google-cloud-firestore \
                google-cloud-storage \
                cachecontrol \
                grpcio \
                firebase-admin \
                download \
                text-unidecode

RUN pip install jupyter_contrib_nbextensions && \
    jupyter contrib nbextension install --user && \
    # can modify or enable additional extensions here
    jupyter nbextension enable spellchecker/main --user

RUN pip install jupyter_dashboards && \
    jupyter dashboards quick-setup --sys-prefix

# Data Science Version Control or DVC is an open-source tool for data science and machine learning projects [https://github.com/iterative/dvc](https://github.com/iterative/dvc)
RUN  pip install --quiet -U --upgrade-strategy only-if-needed --no-cache-dir \
    dvc["ssh"]

## Install from requirements.txt file
## pip freeze >amasing_requirements.txt
#COPY amasing_requirements.txt /tmp/
#RUN pip install --requirement /tmp/amasing_requirements.txt && \
#    fix-permissions $CONDA_DIR && \
#    fix-permissions /home/$NB_USER

# conda list --export > amasing_exported_requirements.txt  # or
# conda env export > amasing_env_requirements.yaml

#COPY amasing_exported_requirements.txt /tmp/

#RUN conda install --yes --file /tmp/amasing_exported_requirements.txt && \
#    conda install --yes jupyter_dashboards -c conda-forge &&  \

USER $NB_USER

RUN conda install --yes papermill -c conda-forge


#RUN conda update --all --yes  &&  \
#    fix-permissions $CONDA_DIR && \
#    fix-permissions /home/$NB_USER
# [Enable nbextension spellchecker for markdown (or any other nbextension)](https://jupyter-docker-stacks.readthedocs.io/en/latest/using/recipes.html#enable-nbextension-spellchecker-for-markdown-or-any-other-nbextension)
