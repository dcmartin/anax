# `QUICKSTART.md`

## 1. Install requisite software:

```
sudo apt update -qq -y && sudo apt install -qq -y ssh git jq curl build-essential docker-compose
```

## 1. Add user to requisite groups:

```
sudo addgroup ${USER} docker
```

## 1. Install Go programming language

```
ARCH=$(uname -m | sed -e 's/aarch64.*/arm64/' -e 's/x86_64.*/amd64/' -e 's/armv.*/armv6l/')
curl -sSL https://golang.org/dl/go1.14.7.linux-${ARCH}.tar.gz | sudo tar xzf - -C /usr/local
```

## 1. Then clone the repository into your selected (`ANAX_SOURCE`) directory:

```
export ANAX_SOURCE=${HOME}/GIT/anax
mkdir -p ${ANAX_SOURCE} && git clone http://github.com/open-horizon/anax.git ${ANAX_SOURCE}
```

## 1. Set environment variables:

```
export PATH=$PATH:/usr/local/go/bin
export GOPATH=${HOME}/go
```

## 1. Build the agent:

```
mkdir -p ${GOPATH}
cd ${ANAX_SOURCE}
make
```

## 1. Make the packages for Debian

```
make debpkgs
```

Example output:

```
Producing cli/hzn given arch: arm64
dpkg-deb: building package 'horizon-cli' in 'debs/horizon-cli_2.27.0_arm64.deb'.
Producing anax given arch: arm64
The required minimum exchange version is 2.23.0
The preferred exchange version is 2.23.0
dpkg-deb: building package 'horizon' in 'debs/horizon_2.27.0_arm64.deb'.
```

## 1. Copy the packages for distribution, for example using [`dcmartin/open-horizon`](http://github.com/dcmartin/open-horizon) repository:

```
DIR=${HOME}/GIT/open-horizon
mkdir -p ${DIR} 
git clone http://github.com/dcmartin/open-horizon ${DIR}
DIR=${DIR}/exchange/pkg/debian 
mkdir -p ${DIR} 
ARCH=$(uname -m | sed -e 's/aarch64.*/arm64/' -e 's/x86_64.*/amd64/' -e 's/armv.*/arm/')
cp ${ANAX_SOURCE}/pkg/deb/debs/{horizon_2.27.0,horizon-cli_2.27.0}_${ARCH}.deb ${DIR}
```

# TEST
Test the agent using local `microk8s`

## 1. Install `microk8s`, set privileges, initiate new login session w/ privileges:

```
sudo snap install microk8s --classic --channel=1.18/stable
sudo addgroup ${USER} microk8s
login ${USER}
```

## 1. Then reboot; log back into machine; set environment variables:

```
export PATH=$PATH:/usr/local/go/bin
export GOPATH=${HOME}/go
export ANAX_SOURCE=${HOME}/GIT/anax
export HZN_ORG_ID="userdev"
export HZN_EXCHANGE_USER_AUTH="userdev/userdevadmin:userdevadminpw"
export HZN_EXCHANGE_URL=http://localhost:8090/v1
export HZN_FSS_CSSURL=https://localhost:9443
export HZN_SSL_SKIP_VERIFY=1
```

## 1. Test the previously built software:

```
sudo chown -R ${USER} ~${USER}/.kube/
cd ${ANAX_SOURCE}/test
make test TEST_VARS="NOLOOP=1 TEST_PATTERNS=sall"
```

