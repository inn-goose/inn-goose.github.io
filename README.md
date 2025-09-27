# goose.sh

## hugo

```
hugo new site goose.sh --format yaml
cd goose.sh/
git init
git submodule add https://github.com/adityatelange/hugo-PaperMod.git themes/hugo-PaperMod
echo "theme: [\"hugo-PaperMod\"]" >> hugo.yaml
```

```
hugo server -D
```

```
git submodule add https://github.com/inn-goose/content.git ./content
```



### Blowfish

```
hugo new site goose.sh --format yaml

cd goose.sh/

git init

git submodule add https://github.com/nunocoracao/blowfish.git themes/blowfish

echo "theme: \"blowfish\"" >> hugo.yaml

hugo server -D
```