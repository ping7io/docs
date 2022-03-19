# ping7.io documentation

This repository holds the information for the `docs.ping7.io` website.

## Running the docs locally

```bash
$ docker build -t ping7-docs .
$ docker run -p 4000:4000 \
    -v $(pwd):/usr/src/app ping7-docs jekyll serve --watch
```

> If you get any obscure Ruby errors, delete the `Gemfile.lock` file.
