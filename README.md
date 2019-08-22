# pernosco-submit

Submits rr traces to Pernosco for debugging.

## Configuration

The following environment variables must be set:
* `PERNOSCO_USER`: User ID in email address form.
* `PERNOSCO_USER_SECRET_KEY`: Secret key. Be careful about sharing this.
* `PERNOSCO_GROUP`: Group to upload to, e.g. `public`. Influences who can access the trace and who gets billed.

The `openssl`, `tar`, `zstdmt` and `aws` tools must be installed. The best way to install `aws` is
```
sudo pip3 install awscli --upgrade
```
Ubuntu and Debian packages [can have issues](https://github.com/aws/aws-cli/issues/2403).

You must use rr master, at least commit [a7e955493673a5e9b43d84bb34da5a8143843c91](https://github.com/mozilla/rr/commit/a7e955493673a5e9b43d84bb34da5a8143843c91).

## Usage

Run
```
pernosco-submit upload <rr-trace-dir> [<source-dir>...]
```
The rr trace directory will be packaged and uploaded. This may take a while and send many gigabytes of data to Amazon S3.

`pernosco-submit` scans the trace to identify relevant source files. For source files in git or Mercurial repositories that `pernosco-submit` knows about, whose plaintext source can be loaded from some Web server, `pernosco-submit` adds metadata to the upload so that the Pernosco client will load the source directly from that server. (PRs accepted to extend that support!) For source files not in a supported repository, or generated during the build, or modified locally, `pernosco-submit` will upload the file as part of the submission *only if* the file's `realpath` is under one of the listed `<source-dir>`s. This is to reduce the likelihood of `pernosco-submit` accidentally uploading confidental files. Thus, if you want locally modified, non-repository, or generated files to be available in the Pernosco client, you must whitelist relevant directories by passing them as parameters to `pernosco-submit`.
