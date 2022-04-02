# winget-push-test
# ================

Push winget packages to a test winget repository using Github workflow instead of pushing to the [main winget repository](https://github.com/microsoft/winget-pkgs) via workflow or directly to [main winget repository](https://github.com/microsoft/winget-pkgs) is supported too.

## Examples

Case-1 (Just an url)
```yaml
steps:
  - name: Submit to winget repository #Name of the workflow step
    uses: gnpaone/winget-push-test@v1
    with:
      url: https://github.com/test-user/test-repo/releases/download/v1.0.0/test-url.exe #Package url
      version: 1.0.0 #Package version
      token: ${{ secrets.GITHUB_TOKEN }} #Token used to submit to Winget repository
      packageid: test.package #Package ID
      user: microsoft #Github username where test winget repo exists
      repo: winget-pkgs #Name of winget test repo
    id: submit  # The step ID to refer to later if needed.
```

Case-2 (Just a normal and arm url)
```yaml
steps:
  - name: Submit to winget repository #Name of the workflow step
    uses: gnpaone/winget-push-test@v1
    with:
      url: https://github.com/test-user/test-repo/releases/download/v1.0.0/test-url.exe #Package url
      urlarm: https://github.com/test-user/test-repo/releases/download/v1.0.0/test-urlarm.exe #Package url (arm)
      version: 1.0.0 #Package version
      token: ${{ secrets.GITHUB_TOKEN }} #Token used to submit to Winget repository
      packageid: test.package #Package ID
      user: microsoft #Github username where test winget repo exists
      repo: winget-pkgs #Name of winget test repo
    id: submit  # The step ID to refer to later if needed.
```

Case-3 (Specifically 32-bit and 64-bit url)
```yaml
steps:
  - name: Submit to winget repository #Name of the workflow step
    uses: gnpaone/winget-push-test@v1
    with:
      url32: https://github.com/test-user/test-repo/releases/download/v1.0.0/test-url32.exe #Package url (32-bit)
      url64: https://github.com/test-user/test-repo/releases/download/v1.0.0/test-url64.exe #Package url (64-bit)
      version: 1.0.0 #Package version
      token: ${{ secrets.GITHUB_TOKEN }} #Token used to submit to Winget repository
      packageid: test.package #Package ID
      user: microsoft #Github username where test winget repo exists
      repo: winget-pkgs #Name of winget test repo
    id: submit  # The step ID to refer to later if needed.
```

Case-4 (Specifically 32-bit and 64-bit url and an arm url)
```yaml
steps:
  - name: Submit to winget repository #Name of the workflow step
    uses: gnpaone/winget-push-test@v1
    with:
      url32: https://github.com/test-user/test-repo/releases/download/v1.0.0/test-url32.exe #Package url (32-bit)
      url64: https://github.com/test-user/test-repo/releases/download/v1.0.0/test-url64.exe #Package url (64-bit)
      urlarm: https://github.com/test-user/test-repo/releases/download/v1.0.0/test-urlarm.exe #Package url (arm)
      version: 1.0.0 #Package version
      token: ${{ secrets.GITHUB_TOKEN }} #Token used to submit to Winget repository
      packageid: test.package #Package ID
      user: microsoft #Github username where test winget repo exists
      repo: winget-pkgs #Name of winget test repo
    id: submit  # The step ID to refer to later if needed.
```

Case-5 (Specifically 32-bit, 64-bit, armhf and arm64 url)
```yaml
steps:
  - name: Submit to winget repository #Name of the workflow step
    uses: gnpaone/winget-push-test@v1
    with:
      url32: https://github.com/test-user/test-repo/releases/download/v1.0.0/test-url32.exe #Package url (32-bit)
      url64: https://github.com/test-user/test-repo/releases/download/v1.0.0/test-url64.exe #Package url (64-bit)
      urlarmhf: https://github.com/test-user/test-repo/releases/download/v1.0.0/test-urlarmhf.exe #Package url (armhf)
      urlarm64: https://github.com/test-user/test-repo/releases/download/v1.0.0/test-urlarm64.exe #Package url (arm64)
      version: 1.0.0 #Package version
      token: ${{ secrets.GITHUB_TOKEN }} #Token used to submit to Winget repository
      packageid: test.package #Package ID
      user: microsoft #Github username where test winget repo exists
      repo: winget-pkgs #Name of winget test repo
    id: submit  # The step ID to refer to later if needed.
```
[Find usages in the wild!](https://github.com/search?l=YAML&q=%22gnpaone%2Fwinget-push-test%22&type=Code)

## Usage

### Inputs

* 1. **`url: PackageURL`**
  2. **`url: PackageURL`** **`urlarm: PackageURLarm`**
  3. **`url32: PackageURLx86`** **`url64: PackageURLx64`**
  4. **`url32: PackageURLx86`** **`url64: PackageURLx64`** **`urlarm: PackageURLarm`**
  5. **`url32: PackageURLx86`** **`url64: PackageURLx64`** **`urlarmhf: PackageURLarmhf`** **`urlarm64: PackageURLarm64`**

  **Required.** Link for your winget package.

* **`version: Version`**

  **Required.** Version of your winget package. Recommended to not keep any letter behind version number (can replace using `Replace` function)

* **`token: SubmitToken`** (default - `${{ secrets.GITHUB_TOKEN }}`)

  Token required to submit package to winget repo.

* **`packageid: PackageID`**

  **Required.** Package ID of your winget package.

* **`user: Username`** (default - `microsoft` where original [winget-pkgs](https://github.com/microsoft/winget-pkgs) repo exists)

  Github username where the test winget repo is created.

* **`repo: RepoName`** (default - `winget-pkgs`)

  Winget test repository name.
  
  ### It's recommended to [clone](https://github.com/microsoft/winget-pkgs/generate) the [original repository](https://github.com/microsoft/winget-pkgs) instead of creating a fresh one for test winget repository purpose.

* **`forcearch: true`** (default is `forcearch: false`)

  Force the submitting architecture for the given package URLs. **Only supported for Case-3 and Case-5 as of now.**

Apart from these all other codes will work as usual for eg getting url and version number
```yaml
steps:
  - name: Get package URL and version
    run: |
      $github = Get-Content '${{ github.event_path }}' | ConvertFrom-Json
      $installerUrl = $github.release.assets | Where-Object -Property name -match ${env:packageFileName} | Select -ExpandProperty browser_download_url -First 1
      $version = $github.event.release.tag_name.Replace('v', '') #if "v" is present in tag name, or else '$github.event.release.tag_name'
```
