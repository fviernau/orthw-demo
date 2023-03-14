Pre-requisites:

1. Remove all package curations and configurations

## Pick a scan to investigate 

```
mkdir -p support/vehicle-app-cpp-sdk && cd support/vehicle-app-cpp-sdk
orthw init https://github.com/fviernau/orthw-demo/raw/main/scan-results/scan-result.json.xz
orthw report-html
firefox report.html
```

## Setup excludes in `ort.yml`

* Project excludes
  1. Show list of projects in HTML report
  2. Find that `requirements.txt` is only for testing
     * https://github.com/eclipse-velocitas/vehicle-app-cpp-sdk/blob/cd8ca56cb845b076373a2862810af427aa6692b2/requirements.txt#L16-L20
  3. Setup a path exclude
     ```
     excludes:
       paths:
       - pattern: "requirements.txt"
         reason: BUILD_TOOL_OF
     ```     
  4. Recompute report
     ```
     orthw report-html
     ```
* Scope excludes can be generated, not applicable in this scan.

## Show list of offending packages
```
orthw offending-packages && orthw offending-packages | wc -l
```

## Showing detected licenses for a package

```
    orthw offending-licenses Conan::c-ares:1.18.1 .
    orthworth offending-licenses Conan::c-ares:1.18.1
    orthw licenses Conan::c-ares:1.18.1 .
    orthw licenses Conan::c-ares:1.18.1
```
Explanations:
* reference indicator: Allows investigating only distinct findings.
* included indicator: Shows the excluded state
* The dot: source code dir to resolve the texts
  * not provided: download
  * wrong dir provided: no resolved license texts
  * provided: resolved license texts without download

## Fixing detected license issues with path excludes

1. Show offending licenses
   ```
   orthw offending-licenses Conan::c-ares:1.18.1 .
   ````
2. Generate package configuration and show it
   ```
   orthw pc-create Conan::c-ares:1.18.1
   code /home/frank/devel/ort/ort-config/package-configurations/Conan/_/c-ares/1.18.1/source-artifact.yml
   ```
3. Show effect HTML report
   ```
   orthw report-html
   firefox report.html
   ```
   * Detected licenses of Conan::c-ares:1.18.1 are greyed out but some GPL is left
4. Show remaining offending licenses
   ```
   orthw offending-licenses Conan::c-ares:1.18.1 .
   ````
5. Add another path exclude at the bottom
   ```
   - pattern: "c-ares-1.18.1/missing"
     reason: "BUILD_TOOL_OF"    
   ```
6. Sort the entries
   ```
   orthw pc-sort Conan::c-ares:1.18.1
   ```
7. Show effect in HTML report        
   ```
   orthw report-html
   firefox report.html
   ```
   * Remaining detected license grayed out.
   * No more open issues besides the get_opt one. It cannot be excluded   

## Batch generate path excludes

1. Commit config repo changes first to not overwrite!!!
2.
   ```
   orthw offending-packages && orthw offending-packages | wc -l
   orthw pc-create-offending
   orthw offending-packages && orthw offending-packages | wc -l
   ```

## Adding copyright garbage

Edit copyrights.txt to keep only (c) self.assertEqual http://ns1 
```
orthw copyrights
code copyrights.txt
orthw export-copyright-garbage
```