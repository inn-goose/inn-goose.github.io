# goose.sh

## hugo

### Local Test

```bash
# compile
hugo

# start a web-server
# load drafts
hugo server -D
```

http://localhost:1313/


### Update Content

```bash
cd ./content

git checkout main

git pull

cd -
```


### Update Blowfish Theme

```bash
cd ./themes/blowfish/

git checkout main

git pull

cd -
```