### Github Workflows Vulnerabilities

`unsafe.yml` contains a workflow that uses `${{ ... }}` in the run script. This can be exploited by malicious actors by sending in executable scripts as payload. I safely tried this out with curl: 

```
curl -X POST -H "Authorization: token $GITHUB_TOKEN" \
     -H "Accept: application/vnd.github.v3+json" \
     https://api.github.com/repos/fatima2003/GitHub-Workflows-Playground/actions/workflows/safe.yml/dispatches \
     -d '{
          "ref": "main",
          "inputs": {
            "canthackme": "\"$(echo MALICIOUS PAYLOAD EXECUTED! && echo SECOND && echo THIRD)\""
          }
```

This was logged: 
```
doing a thing: MALICIOUS PAYLOAD EXECUTED! SECOND THIRD
```

VS in the `safe.yml` where the value is first stored as a local variable in env: 
```
doing a thing: "$(echo MALICIOUS PAYLOAD EXECUTED! && echo SECOND && echo THIRD)"
```

The reason why the latter does not execute the statement is because the ENV parses everything as a String.
