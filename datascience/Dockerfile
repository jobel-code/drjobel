# -*- mode: ruby -*-
# vi: set ft=ruby :

FROM jupyter/datascience-notebook



# Install Python3 packages that aren't available or up-to-date in Conda.
RUN  pip install --quiet -U --upgrade-strategy only-if-needed --no-cache-dir \
                bs4 \
                ggplot \
                lime \
                s2sphere \
                jxmlease

## Install from requirements.txt file
## pip freeze >amasing_requirements.txt
#COPY amasing_requirements.txt /tmp/
#RUN pip install --requirement /tmp/amasing_requirements.txt && \
#    fix-permissions $CONDA_DIR && \
#    fix-permissions /home/$NB_USER

# conda list --export > amasing_exported_requirements.txt  # or
# conda env export > amasing_env_requirements.yaml

COPY amasing_exported_requirements.txt /tmp/
RUN conda install --yes --file /tmp/amasing_exported_requirements.txt && \
    fix-permissions $CONDA_DIR && \
    fix-permissions /home/$NB_USER


# [Enable nbextension spellchecker for markdown (or any other nbextension)](https://jupyter-docker-stacks.readthedocs.io/en/latest/using/recipes.html#enable-nbextension-spellchecker-for-markdown-or-any-other-nbextension)

USER $NB_USER

RUN pip install jupyter_contrib_nbextensions && \
    jupyter contrib nbextension install --user && \
    # can modify or enable additional extensions here
    jupyter nbextension enable spellchecker/main --user

RUN pip install jupyter_dashboards faker && \
    jupyter dashboards quick-setup --sys-prefix
