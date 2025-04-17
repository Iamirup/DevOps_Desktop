#### For bcrypt encryption (recommended):
```bash
cmd: htpasswd -nbB user pass
out: user:$2y$05$Of8eH8lRzvcW33ihFl7lUuvKXE04xpBhcrpjgc8QbRD1O4hYPgAoq
```

#### For SHA1 hash:
```bash
cmd: htpasswd -nbs user pass
out: user:{SHA}nU4eI71bcnBGqeO0t9tXvY1u5oQ=
```

#### For Apache's MD5-based algorithm (APR1):
```bash
cmd: htpasswd -nb user pass
out: user:$apr1$K1TOV1D8$WpzpBI53RUKv92C/cSEHi/
```