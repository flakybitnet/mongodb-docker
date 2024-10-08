## Building binaries

Binaries are available [there](https://dist.flakybit.net/mongodb/) and were built against [Debian 12 (bookworm)](https://hub.docker.com/_/debian):
```
apt update
apt install -y build-essential
apt install -y libcurl4-openssl-dev libssl-dev liblzma-dev
apt install -y python3 python3-venv python-dev-is-python3

mkdir src
cd src
apt install -y git
git clone --depth 1 --branch <version> https://github.com/mongodb/mongo.git
cd  mongo

apt install -y wget
wget https://gitea.flakybit.net/flakybit/mongodb-docker/raw/branch/main/0001-Compile-without-debug-symbols.patch
patch < 0001-Compile-without-debug-symbols.patch

python3 -m venv .venv --prompt mongo
source .venv/bin/activate
python3 -m pip install -r etc/pip/compile-requirements.txt

python3 buildscripts/scons.py install-servers --config=force \
    -j12 \
    --opt=on \
    --release \
    --dbg=off \
    --linker=gold \
    --disable-warnings-as-errors \
    --variables-files=etc/scons/developer_versions.vars \
    --experimental-optimization=-sandybridge

apt install -y tar
cd build/install/bin/
tar -cvzf mongo.tar.gz mongod mongos
```

## Links

1. [Building MongoDB](https://github.com/mongodb/mongo/blob/master/docs/building.md)
2. [Building Mongodb without avx](https://github.com/GermanAizek/mongodb-without-avx/)
