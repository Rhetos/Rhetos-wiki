This article applies to Rhetos v5 and later versions.
For older versions see the corresponding article for [Rhetos v4 and older](Releasing-a-new-Rhetos-version-v4).

This article describes the release process for both **Rhetos framework** (<https://github.com/Rhetos/Rhetos>)
and **Rhetos plugins** (most of the other repositories in <https://github.com/Rhetos>).

1. Before you start:
    * [ ] Make sure you are working on the latest version of the master branch (git pull).
    * [ ] Check out the [project network](https://github.com/Rhetos/Rhetos/network)
      and [pull requests](https://github.com/Rhetos/Rhetos/pulls) for any forgotten branches.
    * [ ] Check the last [release number](https://github.com/Rhetos/Rhetos/releases) and decide on the new version number.
      A new release usually just increases the minor version by 1 (the second number).
      * Note: The version number format must be compliant with [semantic versioning](https://semver.org/).
    * [ ] List any other plugin packages that also need to be released along with this release.
2. Build:
    * [ ] Update *ChangeLog.md* file based on the commit history since the previous release.
    * [ ] Set the release version number in *Build.bat* file (probably it is already set), and the *Prerelease* to an empty value:
      ```text
      SET Prerelease=
      ```
    * [ ] Run full build in the command prompt: `call Clean.bat && call Build.bat && call Test.bat`.
      * Some Rhetos packages don't contain Clean.bat or Test.bat.
    * [ ] Verity that the build is successful: the console output should end with "Test.bat SUCCESSFULLY COMPLETED.".
3. Publish:
    * [ ] Commit your repository changes, **except Build.bat file**, with comment "Release &lt;NEW VERSION&gt;.".
      For example "Release 1.2.0.".
      * Note: If there is nothing to commit, simply do the next step on the last commit.
    * [ ] In your repository create a new tag "v&lt;NEW VERSION&gt;" at the last commit ("Release ...").
      For example "v1.2.0".
    * [ ] Push your repository to GitHub (set the option *Include Tags*).
    * [ ] Publish the generated NuGet package(s) to the public [NuGet gallery](https://www.nuget.org/packages/manage/upload) from the *dist* or *Install* subfolder.
    * [ ] Create a release on GitHub: Open [tags on GitHub](https://github.com/Rhetos/Rhetos/tags),
      at the tag for the newly released version click "Create release".
      * At *Describe this release* write:
        ```text
        See Release notes in [ChangeLog.md](https://github.com/Rhetos/Rhetos/blob/master/ChangeLog.md).
        Rhetos NuGet files are available at [nuget.org](https://www.nuget.org/packages?q=rhetos).
        ```
      * Click "Publish release".
4. Prepare the code for further development:
    * [ ] In *Build.bat* increase the second version number by 1 and set the third to 0
          (for example from 1.2.5 to 1.3.0). Set the `Prerelease` version to `auto`,
          so that the source is ready for the development of the next release:
      ```text
      SET Version=<CURRENT +0.+1.0>
      SET Prerelease=auto
      ```
    * [ ] Run *Build.bat*.
    * [ ] Commit with comment "Development &lt;NEXT VERSION&gt;.". For example "Development 1.3.0.".
    * [ ] Push the repository to GitHub (set the option *Include Tags*).
