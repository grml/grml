---
name: Release checklist
about: Release checklist (for internal use)
title: Release checklist 20YY.MM
labels: ''
assignees: ''

---

<!--
Release checklist guide:

* The tasks in the section "Release Kick-Off" needs to be completed on issue creation.
* Replace YY and MM in the description (with the values from the "New release")
* Replase $PROJECTID with the GitHub project ID
* Remove the comments from the tasks (once they are completed)
* The tasks in section "Preflight" should be completed after the issue has been created.
* The tasks in section "Release Day" should be completed on release day.
* The tasks in section "Announce Release" should be completed soon after the release.
* The tasks in section "Post Release Day" should be completed after the release.

Terminology (with examples):

* Old-Latest release: 2024.12
* Latest release: 2025.05
* Old next release: 2025.Q2 (the project name of release before it was renamed to the "New release" datename)
* New release: 2025.08 (the release we are working on)
* Next release: 2025.Q4

* $PROJECTID: The Grml Project ID of the "New Release"
-->

# Release Kick-Off

- [ ] decide: "New release" codename: Link to comment in https://github.com/grml/gsa-doc/issues/8 (*Comment: update the issue, but don't leak here 😉*)
- [ ] decide: "Next release" codename: Link to comment in https://github.com/grml/gsa-doc/issues/8 (*Comment: update the issue, but dont' leak here 😉*)
- [ ] decide: "New release" datename: `20YY.MM`
- [ ] decide: testing or unstable as base: `unstable|testing`
- [ ] decide: pick daily image (from https://gitlab.grml.org/grml/build-daily/-/pipelines)
- [ ] rename "New release" project (*Comment: rename the "Old next release" in [GitHub project](https://github.com/orgs/grml/projects/$PROJECTID) to reflect the "New release" date (i.e. 2025.Q2 -> 2025.08*)

# Preflight

- [ ] create a new project for the "Next release": (*Comment: Use this [template](https://github.com/orgs/grml/projects/9 and link it here as a placeholder to move the issues which will not be included in the new release*)
- [ ] check repo "grml-live" is empty, otherwise empty "grml-live" (`sudo sudo -u deb-www reprepro -b /var/www/deb.grml.org/repo removematched grml-live '*'`)

  * https://deb.grml.org/dists/grml-live/main/binary-amd64/Packages + https://deb.grml.org/dists/grml-live/main/binary-arm64/Packages + https://deb.grml.org/dists/grml-live/main/binary-i386/Packages need to be empty
  * or on `web01`: `sudo sudo -u deb-www reprepro -b /var/www/deb.grml.org/repo list grml-live` returns nothing

- [ ] review [open, ready PRs (without a project set)](https://github.com/search?q=org%3Agrml+type%3Apr+state%3Aopen+draft%3Afalse+no%3Aproject&type=pullrequests) and put into project "New release" or move to "Next release"
- [ ] review [open Issues (without a project set)](https://github.com/search?q=org%3Agrml+state%3Aopen+no%3Aproject&type=issues&ref=advsearch&s=updated&o=desc) and put into project "New release" or move to "Next release"

  (*Comment: Do not set the project of the issue, to keep it in the "Backlog"*)

- [ ] check GitHub project for open issues: https://github.com/orgs/grml/projects/$PROJECTID (*Comment: either try to implement/fix them in the "Preflight" phase or move them to the "Next release"*)
- [ ] all git repos and debs are in sync, check: https://packages.grml.org
- [ ] daily image was built with newest packages (*Comment: how to check that*)
- [ ] remove "Old-Latest" release files + update `index.[de|en].html` from `/var/www/ftp-master.grml.org`
- [ ] prepare build-release job configuration file: (*Comment: i.e: MR for [2025.05 release config](https://gitlab.grml.org/grml/build-release/-/merge_requests/5) + [2025.08 release config](https://gitlab.grml.org/grml/build-release/-/merge_requests/6)*)
- [ ] prepare website update in new branch and create a PR: (*Comment: i.e. PR for [Grml Release 2025.05](https://github.com/grml/grml.org/pull/102) + [Grml Release 2025.08](https://github.com/grml/grml.org/pull/117)*)
  - [ ] update hugo.yaml with current (pre-)release version
  - [ ] Final: /download/
  - [ ] /faq/ (release name)
  - [ ] /screenshots/ (i.e. PR: [screenshots: Update screenshots for Grml release 2024.12](https://github.com/grml/grml.org/pull/66)
  - [ ] /bugs/known/
  - [ ] changelogs/
  - [ ] changelogs/README-grml-20YY.MM
  - [ ] front page: add news entry
- [ ] prepare blog post in branch and create a PR: (*Comment: i.e. PR for [New blogpost: Grml - new stable release 2025.05 available](https://github.com/grml/blog.grml.org/pull/11) + [New blogpost: Grml - new stable release 2025.08 available](https://github.com/grml/blog.grml.org/pull/13)*)

# Release Day

- [ ] trigger [new "Build - Release" pipeline](https://gitlab.grml.org/grml/build-release/-/pipelines/new): (*Comment: Set `USE_CONFIG_FILENAME` to "Next release" datename and link it here. I.e. [release-2025.08 pipeline](https://gitlab.grml.org/grml/build-release/-/pipelines/1126) -> [release-2025.08 pipeline jobs](https://gitlab.grml.org/grml/build-release/-/pipelines/1126/builds)*)

    (*Comment: is it possible to add images to templates?*)

- [ ] marked artifacts of "collect" step in build-release job as Keep
- [ ] ISO tests
- [ ] ISO + release update test at $site (@mika knows what is to be done)
- [ ] copy repos:
  - [ ] add repos for "New release": grml-20YY.MM grml-live-20YY.MM and **commented out** grml-20YY.MM-updates
  - [ ] add updates repo for "Latest release": (uncomment) grml-20YY.MM-updates
  - [ ] repo: copy grml-stable to grml-20YY.MM-updates repo (`sudo reprepro -b /var/www/deb.grml.org/repo copymatched grml-20YY.MM-updates grml-stable '*'`
  - [ ] repo: EMPTY OUT grml-stable ❗❗❗ (`sudo reprepro -b /var/www/deb.grml.org/repo removematched grml-stable '*'`)
  - [ ] repo: copy grml-testing to grml-stable (`sudo reprepro -b /var/www/deb.grml.org/repo copymatched grml-stable grml-testing '*'`)
  - [ ] repo: copy grml-testing to grml-20YY.MM repo (`sudo reprepro -b /var/www/deb.grml.org/repo copymatched grml-2025.08 grml-testing '*'`)
- [ ] build-daily: update config/daily `last_release`: (*Comment: i.e. [config: Update last_release for 2025.08](https://gitlab.grml.org/grml/build-daily/-/merge_requests/22)*)
- [ ] sign + upload to `/var/www/ftp-master.grml.org/` (RC: `devel/`) + to `/var/www/archive.grml.org/htdocs/`:
  - [ ] make sure you have:
    - [ ] `grml*iso`
    - [ ] `grml*netboot.tar`
    - [ ] `grml*sources.tar`
    - [ ] `grml*metadata.tar`
  - [ ] check sha256 sum files created by build job:
    ```
    sha256sum -c SHA256SUMS-20YY.MM *.sha256
    ```
  - [ ] sign SHA256SUMS-20YY.YY:
    ```
    gpg --armor --detach-sign --output SHA256SUMS-20YY.MM.gpg SHA256SUMS-20YY.MM
    ```
  - [ ] sign ISOs to create `*.asc`:
    ```
    for f in *.iso ; gpg --output $f.asc --armor --detach-sig $f
    ```
  - [ ] check `SHA256SUMS-20YY.MM.gpg SHA256SUMS-20YY.MM`
    ```
    sha256sum -c SHA256SUMS-20YY.MM
    gpg --keyid-format long --verify SHA256SUMS-20YY.MM.gpg SHA256SUMS-20YY.MM
    ```
  - [ ] upload `grml*iso grml*tar` (best done via curl directly from gitlab) (*Comment: to where?*)
  - [ ] upload `SHA256SUMS-20YY.MM.gpg *asc` (*Comment: to where?*)
  - [ ] check on ftp-master.g.o sha256 files and gpg-signatures
    ```
    sha256sum -c SHA256SUMS-20YY.MM *.sha256
    gpg --keyid-format long --verify SHA256SUMS-20YY.MM.gpg SHA256SUMS-20YY.MM
    for x in *iso; do gpg --keyid-format long --verify $x.asc $x; done
    ```
  - [ ] update `index.*html` in ftp-master.g.o
- [ ] move all remaining items out of project and close it

# Announce Release

- [ ] wait for mirror sync, check https://mirror.grml.org
- [ ] merge website update PR to master: (*Comment: i.e. PR for [Grml Release 2025.05](https://github.com/grml/grml.org/pull/102) + [Grml Release 2025.08](https://github.com/grml/grml.org/pull/117)*)
- [ ] merge blog post PR: (*Comment: i.e. PR for [New blogpost: Grml - new stable release 2025.05 available](https://github.com/grml/blog.grml.org/pull/11) + [New blogpost: Grml - new stable release 2025.08 available](https://github.com/grml/blog.grml.org/pull/13)*)
- [ ] send to mailing list: (*Comment: i.e. [Grml - new stable release 2025.05 available](https://lists.mur.at/hyperkitty/list/grml-announce@ml.grml.org/thread/2TA7K3B6SVA4VMSGN6TOF2NX5XP2PLPX/) + [Grml - new stable release 2025.08 available](https://lists.mur.at/hyperkitty/list/grml-announce@ml.grml.org/thread/EPGBLAYO2N7EINV7EJUPXT255O3BZBSP/)*)
- [ ] mastodon: (*Comment: [2025.05](https://hachyderm.io/@grmlproject/114511194074491152) + [2025.08](https://hachyderm.io/@grmlproject/115038916407200568)*)
- [ ] bluesky: (*Comment: [2025.05](https://bsky.app/profile/grmlproject.bsky.social/post/3lp77x3xla227) + [2025.08](https://bsky.app/profile/grmlproject.bsky.social/post/3lwjlbsn2ks2z)*)
- [ ] Update IRC topic (*Comment: how?*)
- [ ] Create [issues @ netboot.xyz](https://github.com/netbootxyz/netboot.xyz/issues/new/choose) + PRs to bump Grml version (*Comment: i.e. [netboot.xyz#1661: Update Grml to 2025.08](https://github.com/netbootxyz/netboot.xyz/issues/1661) + [merged/closed PRs](https://github.com/netbootxyz/debian-squash/pulls?q=is%3Apr+is%3Aclosed+grml)*)

# Post Release Day

- [ ] update [release checklist template](https://github.com/grml/grml/blob/master/.github/ISSUE_TEMPLATE/release-checklist.md) with new learnings
