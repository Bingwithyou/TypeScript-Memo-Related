# How to solve "SSL certificate problem: Unable to get local issuer certificate"

When tried to git clone my repository, is says: Unable to get local issuer certificate which should'n appear in _normal_ Internet enivorment

## Cause

There are two potential causes that have been identified for this issue.

1. A Self-signed certificate cannot be verified.
   > Not clear, could be due to China GFW and it's DNS cache poisoning
2. Default GIT crypto backend (Windows clients)

## Resolution

Resolution #1 - Self Signed certificate
Workaround

Tell git to not perform the validation of the certificate using the global option:

```git
git config --global http.sslVerify false
```

> ⚠️ Please be advised disabling SSL verification globally might be considered a security risk and should be implemented only temporarily

Further more reference link: <https://confluence.atlassian.com/bitbucketserverkb/ssl-certificate-problem-unable-to-get-local-issuer-certificate-816521128.html>
