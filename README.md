

# DependabotTargz

This repository is used to test a possible limitation with Dependabot scanning when a local dependency is specified with a binary `tar.gz` which exceeds one (1) megabyte in size.

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
