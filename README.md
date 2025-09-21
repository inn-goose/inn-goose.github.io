# goose.sh

## hugo

```
hugo new site goose.sh --format yaml
cd goose.sh/
git init
git submodule add https://github.com/adityatelange/hugo-PaperMod.git themes/hugo-PaperMod
echo "theme: [\"hugo-PaperMod\"]" >> hugo.yaml
hugo server --buildDrafts
hugo new content content/posts/about.md
```
