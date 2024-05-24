# Making locally-trusted development certificates.

> A simple zero-config tool to make locally trusted development certificates with any names you'd like.

## Install guides

```shell
$ brew install mkcert
$ brew install nss # if you use Firefox
```

```shell
$ mkcert -CAROOT
$ export NODE_EXTRA_CA_CERTS="$(mkcert -CAROOT)/rootCA.pem"
```

## Note

Created a new certificate valid for the following names

```shell
mkcert 127.0.0.1 localhost dev.domain.com ::1

```

## Reference links

- [mkcert](https://github.com/FiloSottile/mkcert)
