# Setup Privatebin on k3s
## Prepare config
```
# Project namespace
NAMESPACE=privatebin

# Public URL
FQDN=tmp.app.bitbull.ch
```

## create runtime config from template
```
test -d runtime && echo ERROR remove ./runtime dir first
test -d ./runtime && exit 1
mkdir ./runtime
chmod 700 ./runtime
for y in *.yml
do
  cp -av $y ./runtime/$y
  sed -i "s/__NAMESPACE__/$NAMESPACE/g"      ./runtime/$y
  sed -i "s/__FQDN__/$FQDN/g"                ./runtime/$y
done

echo "# current vars
NAMESPACE=$NAMESPACE
FQDN=$FQDN
" > ./runtime/env.sh

ls -l ./runtime

```
## setup application
```
echo "
DONE

all templates created in ./runtime
please review them and apply config

for y in *.yml
do
  echo \$y
  kubectl apply -f \$y
done"

```
## configure application once
This is needed to create permanent config in data volume

* `kubectl exec -it pirvatebin-xyz -- /bin/bash`

```
test -f /srv/data/cfg || cp -rv /srv/cfg /srv/data/cfg
echo '
[main]
name = "BitBin"
fileupload = true
sizelimit = 104857600
[model]
class = Filesystem
[model_options]
dir = PATH "data"' > /srv/data/cfg/conf.php
```
