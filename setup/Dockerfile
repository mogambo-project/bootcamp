FROM jenkins/jenkins:lts
USER root
RUN apt-get update && apt-get install -y lsb-release libatomic1
RUN curl -fsSLo /usr/share/keyrings/docker-archive-keyring.asc \
  https://download.docker.com/linux/debian/gpg
RUN echo "deb [arch=$(dpkg --print-architecture) \
  signed-by=/usr/share/keyrings/docker-archive-keyring.asc] \
  https://download.docker.com/linux/debian \
  $(lsb_release -cs) stable" > /etc/apt/sources.list.d/docker.list
RUN apt-get update && apt-get install -y docker-ce-cli zip
# --- SOPS + age for the secret encrypt/decrypt Jenkins jobs ---
ARG SOPS_VERSION=3.9.4
RUN apt-get update \
  && apt-get install -y age \
  && ARCH="$(dpkg --print-architecture)" \
  && curl -fsSL -o /usr/local/bin/sops \
       "https://github.com/getsops/sops/releases/download/v${SOPS_VERSION}/sops-v${SOPS_VERSION}.linux.${ARCH}" \
  && chmod +x /usr/local/bin/sops \
  && rm -rf /var/lib/apt/lists/* \
  && sops --version && age --version
# --- kubectl for the redeploy job (matches kind cluster v1.31) ---
ARG KUBECTL_VERSION=v1.31.0
RUN ARCH="$(dpkg --print-architecture)" \
  && curl -fsSL -o /usr/local/bin/kubectl \
       "https://dl.k8s.io/release/${KUBECTL_VERSION}/bin/linux/${ARCH}/kubectl" \
  && chmod +x /usr/local/bin/kubectl \
  && kubectl version --client=true
USER jenkins
RUN jenkins-plugin-cli --plugins "blueocean docker-workflow"
