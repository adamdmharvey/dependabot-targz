

# DependabotTargz

This repository is used to test a possible limitation with Dependabot scanning when a local dependency is specified with a binary `tar.gz` which exceeds one (1) megabyte in size.

Essentially, it involves,

1. A code pattern that involves an Nx monorepo with a local dependency pointing to a binrary `tar.gz` file > 1 megabyte
2. Turning on Dependabot to scan for the packages and open pull requests
3. Reviewing the scan output through the path https://github.com/<org>/<repo>/network/updates

The goal is to attempt to reproduce an error like this,

> updater | ERROR <job_312371757> GET https://api.github.com/repos/<ORG>/<REPO>/contents/<PATH>/<FILENAME>.tar.gz?ref=<refid>: 403 - This API returns blobs up to 1 MB in size. The requested blob is too large to fetch via the API, but you can use the Git Data API to request blobs up to 100 MB in size.
updater | <job_312371757> Error summary:
updater | <job_312371757>   resource: Blob
updater | <job_312371757>   field: data
updater | <job_312371757>   code: too_large // See: https://docs.github.com/rest/reference/repos#get-repository-content
```

## Creating the content

To generate this repo, I used the following command:

```shell
npx create-nx-workspace dependabot-targz
```

Following that, I created a "fake" dependency. Essentially, it's an empty raw initiatlized Node.js app with a `package.json` and a large,
random text file, zipped up.

```shell
mkdir apps/dependency-targz/large-dependency
cd apps/dependency-targz/large-dependency
base64 /dev/urandom | head -c 2000000 > large-text-file.txt
# generate an empty app, accepting all defaults, so it will generate a `package.json`
npm init
cd ..
# create our local dependency in the monorepo
tar -czvf large-dependency.tar.gz large-text-file.txt
# clean up
rm -rf large-dependency/
```

Finally, add this package to the main monorepo's dependency tree:

```shell
npm install --save apps/dependabot-targz/large-dependency.tar.gz
```
