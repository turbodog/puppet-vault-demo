FROM alpine:3.8
WORKDIR /usr/src

RUN apk add --no-cache \
      ca-certificates \
      pciutils \
      ruby \
      ruby-irb \
      ruby-rdoc \
      ruby-etc \
      shadow \
      git \
 && gem install --no-rdoc --no-ri rake rspec

RUN git clone https://github.com/puppetlabs/puppet --branch vault /usr/src/puppet

WORKDIR /usr/src/puppet

RUN rake package:bootstrap \
 && rake package:gem \
 && gem install --no-rdoc --no-ri pkg/puppet-$(rake package:version | tr - .).gem \
 && gem install --no-rdoc --no-ri vault
