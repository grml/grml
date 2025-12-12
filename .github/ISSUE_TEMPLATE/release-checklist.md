---
name: Release checklist
about: Release checklist (for internal use)
title: Release checklist 20YY.MM
labels: ''
assignees: ''

---

<!--
"After the release is before the release": Create a new "release checklist" issue as soon as possible after a Grml release.

Release checklist guide:

* Replace 20YY.MM ("New release" datename) / 20YY.BB (Latest release datename) in the description
* Replace $PROJECTID with the GitHub project ID of the upcoming release
* Replace $LASTRELEASEDATE in the search queries below
* Replace $RELEASEDATE in the search queries below (usually just a guess)
* Remove the comments from the tasks (once they are completed)
* The tasks in section "Preflight" should be completed after the issue has been created.
* The tasks in section "Preflight" should be completed after the issue has been created.
* The tasks in section "Release Day" should be completed on release day.
* The tasks in section "Announce Release" should be completed soon after the release.
* The tasks in section "Post Release Day" should be completed after the release.

Terminology (with examples):

* Last release date: 2024-12-20
* Old-Latest release: 2024.12
* Latest release: 2025.05
* Old next release: 2025.Q2 (the project name of the release before it was renamed to the "New release" datename)
* Release date: 2024-08-16
* New release: 2025.08 (the release we are working on)
* Next release: 2025.Q4
-->

# Release Kick-Off

- [ ] Latest release datename: `20YY.BB`
- [ ] decide: "New release" codename: Link to comment in https://github.com/grml/gsa-doc/issues/8 (*Comment: update the issue, but don't leak here 😉*)
- [ ] decide: "Next release" codename: Link to comment in https://github.com/grml/gsa-doc/issues/8 (*Comment: update the issue, but dont' leak here 😉*)
- [ ] decide: "New release" datename: `20YY.MM`
- [ ] decide: Grml Release Meetup date and location
- [ ] decide: testing or unstable as base: `unstable|testing`
- [ ] rename "New release" project (*Comment: rename the "Old next release" in [GitHub project](https://github.com/orgs/grml/projects/$PROJECTID) to reflect the "New release" date (i.e. 2025.Q2 -> 2025.08) and update $PROJECTID below.*)
- [ ] create a new project for the "Next release": (*Comment: Make a copy of [template](https://github.com/orgs/grml/projects/9, in Settings change the visibility to "Public" and link it here as a placeholder to move the issues which will not be included in the new release.*)

# Preflight

- [ ] check repo "grml-live" is empty, otherwise empty "grml-live" (`sudo sudo -u deb-www reprepro -b /var/www/deb.grml.org/repo removematched grml-live '*'`)

  * https://deb.grml.org/dists/grml-live/main/binary-amd64/Packages + https://deb.grml.org/dists/grml-live/main/binary-arm64/Packages + https://deb.grml.org/dists/grml-live/main/binary-i386/Packages need to be empty
  * or on `web01`: `sudo sudo -u deb-www reprepro -b /var/www/deb.grml.org/repo list grml-live` returns nothing

- [ ] review [open, ready PRs (without a project set)](https://github.com/search?q=org%3Agrml+type%3Apr+state%3Aopen+draft%3Afalse+no%3Aproject&type=pullrequests) and put into project "New release" or move to "Next release"
- [ ] review [open Issues (without a project set)](https://github.com/search?q=org%3Agrml+state%3Aopen+no%3Aproject&type=issues&ref=advsearch&s=updated&o=desc) and put into project "New release" or move to "Next release"

  (*Comment: Do not set the project of the issue, to keep it in the "Backlog"*)

- [ ] Triage (new) issues: https://github.com/search?q=org%3Agrml+no%3Aproject&type=issues (*Comment: "New issues" have no project and no label set: set project "Grml Release" -> Accepted or Backlog or set Closed (not planned)*)
- [ ] Optionally re-triage release issues: https://github.com/orgs/grml/projects/16/views/1
- [ ] check GitHub project for open issues: https://github.com/orgs/grml/projects/$PROJECTID (*Comment: either try to implement/fix them in the "Preflight" phase or move them to the "Next release"*)
- [ ] all git repos and debs are in sync, check: https://packages.grml.org
- [ ] daily image was built with newest packages (*Comment: if we change, add or remove any packages, we have to create a new daily image aka pick a pipeline job*)
- [ ] prepare build-release job configuration file: (*Comment: i.e: MR for [2025.05 release config](https://gitlab.grml.org/grml/build-release/-/merge_requests/5) + [2025.08 release config](https://gitlab.grml.org/grml/build-release/-/merge_requests/6)*)
- [ ] review [closed issues since last release date](https://github.com/search?q=org%3Agrml+closed%3A${LASTRELEASEDATE}..${RELEASEDATE}&type=issues&ref=advsearch) (to be included in changelogs)
- [ ] review [closed pull requests since last release date](https://github.com/search?q=org%3Agrml+closed%3A${LASTRELEASEDATE}..${RELEASEDATE}&type=pullrequests&ref=advsearch) (to be included in changelogs)
- [ ] prepare website update + release notes in new branch and create a PR: (*Comment: i.e. PR for [Grml Release 2025.05](https://github.com/grml/grml.org/pull/102) + [Grml Release 2025.08](https://github.com/grml/grml.org/pull/117)*)
  - [ ] update hugo.yaml with current (pre-)release version
  - [ ] Final: /download/
  - [ ] /faq/ (release name)
  - [ ] /screenshots/ (i.e. PR: [screenshots: Update screenshots for Grml release 2024.12](https://github.com/grml/grml.org/pull/66)
  - [ ] /bugs/known/
  - [ ] changelogs/
  - [ ] changelogs/README-grml-20YY.MM
  - [ ] front page: add news entry
- [ ] prepare blog post in branch and create a PR: (*Comment: i.e. PR for [New blogpost: Grml - new stable release 2025.05 available](https://github.com/grml/blog.grml.org/pull/11) + [New blogpost: Grml - new stable release 2025.08 available](https://github.com/grml/blog.grml.org/pull/13)*)
- [ ] review changes ("Changes to Debian package list:") between last daily and the latest release: (*Comment: https://daily.grml.org/grml-full-amd64-testing/ -> `...logs/changes-last-release.txt`*)
- [ ] download and test daily ISOs

# Release Day

- [ ] decide: pick daily image (from https://gitlab.grml.org/grml/build-daily/-/pipelines)
- [ ] trigger [new "Build - Release" pipeline](https://gitlab.grml.org/grml/build-release/-/pipelines/new): (*Comment: Set `USE_CONFIG_FILENAME` to "Next release" datename i.e. `2025.12` and link it here. I.e. [release-2025.08 pipeline](https://gitlab.grml.org/grml/build-release/-/pipelines/1126) -> [release-2025.08 pipeline jobs](https://gitlab.grml.org/grml/build-release/-/pipelines/1126/builds)*)

<img width="1294" height="759" alt="Image" src="https://github.com/user-attachments/assets/12759109-6da8-43fa-8508-e38cd6b63e64" />

- [ ] remove "Old-Latest" release files + update `index.[de|en].html` from `/var/www/ftp-master.grml.org`
- [ ] mark artifacts of "collect" step in build-release job as Keep
- [ ] ISO tests
- [ ] ISO + release update test at $site (@mika knows what is to be done)
- [ ] copy repos:
  - [ ] add repos for "New release": grml-20YY.MM grml-live-20YY.MM and **commented out** grml-20YY.MM-updates
  - [ ] add updates repo for "Latest release": (uncomment) grml-20YY.BB-updates
  - [ ] repo: copy grml-stable to grml-20YY.MM-updates repo (`sudo reprepro -b /var/www/deb.grml.org/repo copymatched grml-20YY.MM-updates grml-stable '*'`
  - [ ] repo: EMPTY OUT grml-stable ❗❗❗ (`sudo reprepro -b /var/www/deb.grml.org/repo removematched grml-stable '*'`)
  - [ ] repo: copy grml-testing to grml-stable (`sudo reprepro -b /var/www/deb.grml.org/repo copymatched grml-stable grml-testing '*'`)
  - [ ] repo: copy grml-testing to grml-20YY.MM repo (`sudo reprepro -b /var/www/deb.grml.org/repo copymatched grml-2025.MM grml-testing '*'`)
- [ ] build-daily: update config/daily `last_release` to to `20YY.MM` https://gitlab.grml.org/grml/build-daily/-/blob/main/config/daily and create a MR (*Comment: i.e. [config: Update last_release for 2025.08](https://gitlab.grml.org/grml/build-daily/-/merge_requests/22)*)
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
  - [ ] upload `grml*iso grml*tar` (best done via curl directly from gitlab)

    * On `web01` download `artifacts.zip`: `curl --location --header "PRIVATE-TOKEN: glpat-XXXX" "https://gitlab.grml.org/api/v4/projects/grml%2Fbuild-release/jobs/7592/artifacts" -O`
    * Unzip `artifacts.zip` and move the files `grml*iso grml*tar` to `/var/www/ftp-master.grml.org/`

  - [ ] upload `SHA256SUMS-20YY.MM.gpg *asc`

    * From the unzipped `artifacts.zip` move the files `SHA256SUMS-20YY.MM.gpg *asc` to `/var/www/ftp-master.grml.org/`

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
- [ ] Update IRC topic (*Comment: #grml channel operator can set the topic with something like this:* `/topic Grml Live Linux | Grml 2025.12 available! -> https://grml.org/ + https://grml.org/changelogs/README-grml-2025.12/ | Daily Snapshots: https://daily.grml.org/ | https://grml.org/faq/ + https://github.com/grml/grml/wiki/`)
- [ ] Create [issues @ netboot.xyz](https://github.com/netbootxyz/netboot.xyz/issues/new/choose) + PRs to bump Grml version (*Comment: i.e. [netboot.xyz#1661: Update Grml to 2025.08](https://github.com/netbootxyz/netboot.xyz/issues/1661) + [merged/closed PRs](https://github.com/netbootxyz/debian-squash/pulls?q=is%3Apr+is%3Aclosed+grml)*)

# Post Release Day

- [ ] update [release checklist template](https://github.com/grml/grml/blob/master/.github/ISSUE_TEMPLATE/release-checklist.md) with new learnings
- [ ] "After the release is before the release": Create a new "release checklist" issue as soon as possible after a Grml release
