---
name: Release checklist
about: Release checklist (for internal use)
title: Release checklist 20YY.MM
labels: ''
assignees: ''

---

# Planning

- [ ] decide: release codename: `TBD`
- [ ] decide: release datename: `20YY.MM`
- [ ] decide: testing or unstable as base: `unstable|testing` -> decided to use `testing`
- [ ] decide: pick daily image: https://gitlab.grml.org/grml/build-daily/-/pipelines/1098/builds - built on trixie release day

# Preflight

- [ ] create new release Project for the next release in https://github.com/orgs/grml/projects using template - https://github.com/orgs/grml/projects/13
- [ ] check repo "grml-live" is empty, otherwise empty it out
- [ ] grml-testing arch-specific packages are gone, only `all` shall remain (verify: `sudo reprepro -b /var/www/deb.grml.org/repo --list-format '${Architecture} ${$identifier} ${package} ${version}\n' list grml-testing | grep -v '^all'`)  - Remove from next release checklist?
- [ ] rename GitHub project for release to reflect release date: https://github.com/orgs/grml/projects/11
- [ ] put relevant open [Ready PRs](https://github.com/search?q=org%3Agrml+state%3Aopen+draft%3Afalse&type=pullrequests&ref=advsearch) into Project
- [ ] put relevant open GitHub [Issues](https://github.com/search?q=org%3Agrml+state%3Aopen&type=Issues&ref=advsearch&l=&l=&s=updated&o=desc) into Project
- [ ] check GitHub project for open topics: https://github.com/orgs/grml/projects/11
- [ ] all git repos and debs are in sync, check https://packages.grml.org
- [ ] daily image was built with newest packages
- [ ] archive $previous_release-2 from /var/www/ftp-master.grml.org to archive.grml.org/htdocs/ - either move or remove if already in archive.g.o folder 
- [ ] copy previous release (ISO, sources, netboot packages) to archive.grml.org/htdocs/
- [ ] prepare build-release job configuration file: [Previous](https://gitlab.grml.org/grml/build-release/-/merge_requests/5)  -- [Current 🚀](https://gitlab.grml.org/grml/build-release/-/merge_requests/6) 
- [ ] prepare website update in branch. [Previous](https://github.com/grml/grml.org/pull/102)
  - [ ] update hugo.yaml with current (pre-)release version
  - [ ] Final: /download/
  - [ ] /faq/ (release name)
  - [ ] /screenshots/ PR: [Previous](https://github.com/grml/grml.org/pull/66)
  - [ ] /bugs/known/
  - [ ] changelogs/
  - [ ] changelogs/README-grml-20XX.MM
  - [ ] front page: add news entry
- [ ] prepare blog post in branch. [Previous](https://github.com/grml/blog.grml.org/pull/11)


# Doing

https://gitlab.grml.org/grml/build-release/-/pipelines/TBD/builds

- [ ] marked artifacts of "collect" step in build-release job as Keep
- [ ] ISO tests
- [ ] ISO + release update test at $site (@mika knows what is to be done)
- [ ] copy repos:
  - [ ] 2025.08 special: switch signing key to only CF55033A https://github.com/grml/grml-keyring/issues/4#issuecomment-2879670828
  - [ ] add repos for new release: grml-YYYY.MM grml-live-YYYY.MM and **commented out** grml-YYYY.MM-updates
  - [ ] add updates repo for old release: (uncomment) grml-YYYY.BB-updates
  - [ ] repo: copy grml-stable to grml-YYYY.BB-updates repo (`sudo reprepro -b /var/www/deb.grml.org/repo copymatched grml-YYYY.BB-updates grml-stable '*'`
  - [ ] repo: EMPTY OUT grml-stable ❗❗❗ 
  - [ ] repo: copy grml-testing to grml-stable
  - [ ] repo: copy grml-testing to grml-YYYY.MM repo
  - [ ] 20YY.MM special: mark https://github.com/grml/grml-live/issues/344 as done
- [ ] build-daily: update config/daily last_release: [MR 🚢 ](https://gitlab.grml.org/grml/build-daily/-/merge_requests/22)
- [ ] sign + upload to `/var/www/ftp-master.grml.org/` (RC: `devel/`):
  - [ ] make sure you have:
    - [ ] grml*iso
    - [ ] grml*netboot.tar
    - [ ] grml*sources.tar
    - [ ] grml*metadata.tar
  - [ ] check sha256 sum files created by build job:
    ```
    sha256sum -c SHA256SUMS-2025.08 *.sha256
    ```
  - [ ] sign SHA256SUMS-2025.08:
    ```
    gpg --armor --detach-sign --output SHA256SUMS-20YY.MM.gpg SHA256SUMS-20YY.MM
    ```
  - [ ] sign ISOs to create *asc:
    ```
    for f in *.iso ; gpg --output $f.asc --armor --detach-sig $f
    ```
  - [ ] check `SHA256SUMS-20YY.MM.gpg SHA256SUMS-20YY.MM`
    ```
    sha256sum -c SHA256SUMS-20YY.MM
    gpg --keyid-format long --verify SHA256SUMS-20YY.MM.gpg SHA256SUMS-20YY.MM
    ```
  - [ ] upload `grml*iso grml*tar` (best done via curl directly from gitlab)
  - [ ] upload `SHA256SUMS-20YY.MM.gpg *asc`
  - [ ] check on ftp-master.g.o sha256 files and gpg-signatures
    ```
    sha256sum -c SHA256SUMS-20YY.MM *.sha256
    gpg --keyid-format long --verify SHA256SUMS-20YY.MM.gpg SHA256SUMS-20YY.MM
    for x in *iso; do gpg --keyid-format long --verify $x.asc $x; done
    ```
  - [ ] update `index.*html` in ftp-master.g.o

# Announce

- [ ] wait for mirror sync, check https://mirror.grml.org
- [ ] merge website branch to master: [Previous](https://github.com/grml/grml.org/pull/102) -- [Current PR 🛳](https://github.com/grml/grml.org/pull/TBD)
- [ ] merge blog branch: [Previous](https://github.com/grml/blog.grml.org/pull/11 ) -- [Current PR 🛳](https://github.com/grml/blog.grml.org/pull/TBD)
- [ ] mailing list: [Previous](https://lists.mur.at/pipermail/grml-announce/2025-May/000062.html)
- [ ] mastodon [Previous](https://hachyderm.io/@grmlproject/114511194074491152)
- [ ] bluesky [Previous](https://bsky.app/profile/grmlproject.bsky.social/post/3lp77x3xla227)
- [ ] IRC topic
- [ ] [netboot.xyz: create issue](https://github.com/netbootxyz/netboot.xyz): [Previous issue](https://github.com/netbootxyz/netboot.xyz/issues/1623) [Previous PRs](https://github.com/netbootxyz/debian-squash/pull/3)
