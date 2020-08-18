# `QUICKSTART.md`

## 1. Install requisite software:

```
sudo apt update -qq -y && sudo apt install -qq -y ssh git jq curl build-essential docker-compose
sudo snap install microk8s --classic --channel=1.18/stable
```

## 1. Add user to requisite groups:

```
sudo addgroup ${USER} docker
sudo addgroup ${USER} microk8s
```

## 1. Install Go programming language

```
wget https://golang.org/dl/go1.14.7.linux-amd64.tar.gz
sudo tar xzf go1.14.7.linux-amd64.tar.gz -C /usr/local/
```

## 1. Then reboot; log back into machine and set environment variables:

```
export PATH=$PATH:/usr/local/go/bin
export GOPATH=${HOME}/go
export HZN_ORG_ID="userdev"
export HZN_EXCHANGE_USER_AUTH="userdev/userdevadmin:userdevadminpw"
export HZN_EXCHANGE_URL=http://localhost:8090/v1
export HZN_FSS_CSSURL=https://localhost:9443
export HZN_SSL_SKIP_VERIFY=1
export ANAX_SOURCE=${HOME}/GIT/anax
```

## 1. Then clone the repository:

```
mkdir -p ${ANAX_SOURCE}
git clone http://github.com/open-horizon/anax.git ${ANAX_SOURCE}
```

## 1. Configure Go and Kubernetes:

```
mkdir -p ${GOPATH}
sudo chown -R ${USER} ~${USER}/.kube/
```

## 1. Build the agent:

```
cd ${ANAX_SOURCE}
make
```

## 1. Test the agent:

```
cd test
make test TEST_VARS="NOLOOP=1 TEST_PATTERNS=sall"
```
