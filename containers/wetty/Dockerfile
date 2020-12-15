FROM node:current-alpine as builder
RUN apk add -U build-base python
WORKDIR /usr/src/app
COPY . /usr/src/app
RUN yarn && \
    yarn build && \
    yarn install --production --ignore-scripts --prefer-offline

FROM node:current-alpine
LABEL maintainer="butlerx@notthe.cloud"
WORKDIR /usr/src/app
ENV NODE_ENV=production
ENV KUBE_VERSION="v1.19.3"
ENV HELM_VERSION="v3.4.2"
EXPOSE 3000
COPY --from=builder /usr/src/app/build /usr/src/app/build
COPY --from=builder /usr/src/app/node_modules /usr/src/app/node_modules
COPY package.json /usr/src/app
RUN apk add -U openssh-client sshpass curl git nano wget && \
    mkdir ~/.ssh && \
	ssh-keyscan -H wetty-ssh >> ~/.ssh/known_hosts && \
    adduser bernd -D --home /home/bernd && \

ADD https://storage.googleapis.com/kubernetes-release/release/${KUBE_VERSION}/bin/linux/amd64/kubectl /usr/local/bin/kubectl

RUN chmod +x /usr/local/bin/kubectl \
    && wget https://get.helm.sh/helm-${HELM_VERSION}-linux-amd64.tar.gz \
    && tar -xvf helm-v${HELM_VERSION}-linux-amd64.tar.gz -C /usr/local/bin \
    && chmod +x /usr/local/bin/linux-amd64/helm \
    && mv /usr/local/bin/linux-amd64/helm /usr/local/bin/ && rm /usr/local/bin/linux-amd64 -rf

ENTRYPOINT [ "yarn" , "docker-entrypoint"]
