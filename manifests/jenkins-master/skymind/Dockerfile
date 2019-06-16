FROM jenkins/jenkins:2.176.1

# Copy jenkins-github-skip-pr-by-title plugin
COPY --chown=jenkins plugins/github-skip-pr-by-title.hpi /usr/share/jenkins/ref/plugins/github-skip-pr-by-title.hpi

# Copy list of requrired plugins
ARG PLUGINS_FILE_NAME=plugins-locked.txt
COPY --chown=jenkins configs/plugins/$PLUGINS_FILE_NAME /usr/share/jenkins/ref/plugins.txt

# Install required plugins
# xargs is a workaround for mkdir: cannot create directory ‘/usr/share/jenkins/ref/plugins/credentials.lock’: File exists
# from https://github.com/jenkinsci/docker/issues/502
RUN xargs /usr/local/bin/install-plugins.sh < /usr/share/jenkins/ref/plugins.txt
