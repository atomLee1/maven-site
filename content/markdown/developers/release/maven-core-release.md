## Releasing Maven

Maven differs slightly in its release process due to several extra steps.

The goal is to commit candidate release to svn tree <https://dist.apache.org/repos/dist/dev/maven/maven-3>/`$VERSION`. Then once the vote passed, svn move to <https://dist.apache.org/repos/dist/release/maven/maven-3>/`$VERSION`.

The tree directory is:

- https://dist.apache.org/repos/dist/release/maven/maven-3/`$VERSION`/binaries
- https://dist.apache.org/repos/dist/release/maven/maven-3/`$VERSION`/source (notice: singular...)

Note that the policy for failed releases is to move forward with a new
version. The tag that produced the failed released is left in place for
posterity. So if the release of, say, 3.2.4 fails then we move forward
with 3.2.5.

### Produce Release Candidates

For non-alpha/beta releases, release candidates are produced before the actual release.

Checkout https://dist.apache.org/repos/dist/dev/maven/maven-3 then create the necessary directory tree.

Copy the binaries and src-tar.gz with their sha512/asc to the created directories.

To produce a release candidate, follow the first seven steps only from the following procedure:

-   [Maven Project Common Release Procedure](./maven-project-release-procedure.html)

The version used should be the eventual version with -RC1, -RC2, etc. appended.

After producing the RC, request that the developers test the release on the list. If a regression is found, a new release candidate is rolled.

After a reasonable time without regressions found, a wider audience may be polled if the release manager desires (for example, users@).

Once happy with a release candidate, the full release is performed, with the final version in place.

### Produce the Release

To produce a final release, the same process as for standard projects is followed:

-   [Maven Project Common Release Procedure](./maven-project-release-procedure.html)

Below describes the additional steps that need to be taken at the points where the website are updated in those instructions.

#### Update the DOAP Information

Edit <https://github.com/apache/maven/blob/master/doap_Maven.rdf> to list the new release.

#### Update the Release Notes and Web Site

Checkout Maven site source <https://github.com/apache/maven-site.git>.

Note that release notes can be created and checked in, but other changes should not be checked in as it can be deployed 'live' at any time.

- For 3.x: update the `versions3x`, `currentStableVersion` and `currentStableReleaseDate` properties in `pom.xml`

Next, create the release notes:

- create `content/markdown/docs/$version`
- create `content/markdowndocs/$version/release-notes.md` (see other versions for an example)

Next, update release history `content/markdown/docs/history.md.vm`.

Only deploy the site once the release is present on the mirrors, and the reference documentation has been deployed to [/ref/](/ref).

#### Stage the Latest Documentation

Once the release is prepared, but before the release vote, the site needs to be staged.

From the release checkout, stage the site:

```
mvn -Preporting site site:stage
mvn scm-publish:publish-scm
```

This will publish the Maven core site in [/ref/3-LATEST](/ref/3-LATEST).

#### Add New Version to ASF Distribution Directory

In addition to promoting the repository, the release archives should be
moved to the release svnpubsub tree:

```
svn mv https://dist.apache.org/repos/dist/dev/maven/maven-3/$VERSION https://dist.apache.org/repos/dist/release/maven/maven-3
```

#### Deploy the Latest Documentation to Target Versioned Location

The reference documentation for Maven core source code references and API docs has been staged in a previous step: now it needs to be deployed to its dedicated directory before deploying the web site pointing to the new version.

This consists in copying in website svn tree the staging /ref/3-LATEST directory to final /ref/`$VERSION`.

```
svn cp https://svn.apache.org/repos/asf/maven/website/components/ref/3-LATEST https://svn.apache.org/repos/asf/maven/website/components/ref/$VERSION
```

### Information on `/ref/current` mechanism

The redirection from `/ref/current` to actual Maven version reference is done through `.htaccess` published in site.

#### Deploying the Release Website

Once both of the above have synced to the main site and a suitable number of mirrors, proceed to update the web site and produce the announcement.

Commit your changes and then [deploy the main Maven site](../website/deploy-maven-website.html).

#### Remove Old Versions from ASF Distribution Directory

Next, any superceded releases should be removed from the above locations (after confirming that they exist in /www/archive.apache.org/dist/maven).

#### Proceed with Announcement

You can now proceed with the steps outlined after deploying the website on [Maven Project Common Release Procedure](./maven-project-release-procedure.html)

