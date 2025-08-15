---
name: Release checklist
about: Release checklist (for internal use)
title: Release checklist 20YY.MM
labels: ''
assignees: ''

---

<!-- 
This release checklist issue should be created once all decicions in section "Planning" have been made.

The tasks in section "Preflight" can be completed instantly.

The tasks in section "Doing" should be completed on release day.

The tasks in section "Announce" should be completed soon after the release.

Terminology (with examples):

Old-Latest release: 2024.12
Latest release: 2025.05
New release: 2025.08 (the release we are working on)
Next release: 2025.Q4
-->

# Planning

- [ ] decide: release codename: Link to comment in https://github.com/grml/gsa-doc/issues/8 (*don't leak here 😉)
- [ ] decide: next release codename: Link to comment in https://github.com/grml/gsa-doc/issues/8 (*dont' leak here 😉)
- [ ] decide: release datename: `20YY.MM`
- [ ] decide: testing or unstable as base: `unstable|testing`
- [ ] decide: pick daily image (from https://gitlab.grml.org/grml/build-daily/-/pipelines)

# Preflight

- [ ] rename the [current GitHub project](https://github.com/orgs/grml/projects/) for release to reflect release date (i.e. 2025.Q2 -> 2025.08)
- [ ] create new release project for the *next* release in https://github.com/orgs/grml/projects using [template](https://github.com/orgs/grml/projects/9) (*placeholder to move the issues which will not be included in the current release*)
- [ ] check repo "grml-live" is empty

* https://deb.grml.org/dists/grml-live/main/binary-amd64/Packages + https://deb.grml.org/dists/grml-live/main/binary-arm64/Packages + https://deb.grml.org/dists/grml-live/main/binary-i386/Packages need to be empty
* or on `web01`: `sudo sudo -u deb-www reprepro -b /var/www/deb.grml.org/repo list grml-live` returns nothing
  - [ ] otherwise empty it out (`sudo sudo -u deb-www reprepro -b /var/www/deb.grml.org/repo removematched grml-live '*'`)

- [ ] put relevant open [Ready PRs](https://github.com/search?q=org%3Agrml+state%3Aopen+draft%3Afalse&type=pullrequests&ref=advsearch) into Project
- [ ] put relevant open GitHub [Issues](https://github.com/search?q=org%3Agrml+state%3Aopen&type=Issues&ref=advsearch&l=&l=&s=updated&o=desc) into Project
- [ ] check GitHub project for open topics: https://github.com/orgs/grml/projects/11
- [ ] all git repos and debs are in sync, check https://packages.grml.org
- [ ] daily image was built with newest packages
- [ ] remove "Old-Latest" release files + update `index.[de|en].html` from `/var/www/ftp-master.grml.org`
- [ ] prepare build-release job configuration file: [Latest](https://gitlab.grml.org/grml/build-release/-/merge_requests/5)  -- [New 🚀](https://gitlab.grml.org/grml/build-release/-/merge_requests/6) 
- [ ] prepare website update in new branch and create a PR (i.e. PR for [Grml Release 2025.05](https://github.com/grml/grml.org/pull/102))
  - [ ] update hugo.yaml with current (pre-)release version
  - [ ] Final: /download/
  - [ ] /faq/ (release name)
  - [ ] /screenshots/ (i.e. PR: [screenshots: Update screenshots for Grml release 2024.12](https://github.com/grml/grml.org/pull/66)
  - [ ] /bugs/known/
  - [ ] changelogs/
  - [ ] changelogs/README-grml-20XX.MM
  - [ ] front page: add news entry
- [ ] prepare blog post in branch and create a PR (i.e. PR for [New blogpost: Grml - new stable release 2025.05 available](https://github.com/grml/blog.grml.org/pull/11))

# Doing

https://gitlab.grml.org/grml/build-release/-/pipelines/TBD/builds

- [ ] marked artifacts of "collect" step in build-release job as Keep
- [ ] ISO tests
- [ ] ISO + release update test at $site (@mika knows what is to be done)
- [ ] copy repos:
  - [ ] add repos for new release: grml-YYYY.MM grml-live-YYYY.MM and **commented out** grml-YYYY.MM-updates
  - [ ] add updates repo for old release: (uncomment) grml-YYYY.BB-updates
  - [ ] repo: copy grml-stable to grml-YYYY.BB-updates repo (`sudo reprepro -b /var/www/deb.grml.org/repo copymatched grml-YYYY.BB-updates grml-stable '*'`
  - [ ] repo: EMPTY OUT grml-stable ❗❗❗ 
  - [ ] repo: copy grml-testing to grml-stable
  - [ ] repo: copy grml-testing to grml-YYYY.MM repo
  - [ ] 20YY.MM special: mark https://github.com/grml/grml-live/issues/344 as done
- [ ] build-daily: update config/daily last_release: [MR 🚢 ](https://gitlab.grml.org/grml/build-daily/-/merge_requests/22)
- [ ] sign + upload to `/var/www/ftp-master.grml.org/` (RC: `devel/`) + to `/var/www/archive.grml.org/htdocs/`:
  - [ ] make sure you have:
    - [ ] grml*iso
    - [ ] grml*netboot.tar
    - [ ] grml*sources.tar
    - [ ] grml*metadata.tar
  - [ ] check sha256 sum files created by build job:
    ```
    sha256sum -c SHA256SUMS-20YY.MM *.sha256
    ```
  - [ ] sign SHA256SUMS-20YY.YY:
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
