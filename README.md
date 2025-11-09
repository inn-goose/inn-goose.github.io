# Website: [Goose in the Shell](https://goose.sh)

This blog is statically generated using:
* [Hugo](https://gohugo.io) static site generator
* [Blowfish](https://blowfish.page) theme for Hugo

Hosted on:
* [GitHub Pages](https://pages.github.com)
* [goose.sh](https://goose.sh)

The content of this site is stored in a separate repository:
* [Content](https://github.com/inn-goose/content) for `inn-goose.github.io`


## Hugo

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