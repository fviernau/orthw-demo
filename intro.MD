## Doing license compliance with ORT

Run the pipeline:

1. Determine dependencies: source code location, declared licenses
2. Scan source code of all dependencies for licenses and copyrights
3. Evaluate policy rules against applicable licenses
4. Present outcome in human readable report

Not all data can be gathered automatically:

- Package metadata incomplete or incorrect
- License detections incorrect
- Copyright detections incorrect
- Unknown which files in the code repository end-up in the released   
  artifact

Augment the data with manual fixes (reduce over-reporting, false positive violations):

- Package curations: correct metadata
- License finding curations: fix incorrect license detections
- Excludes: tell which files or scopes are not included in release artifact
- Copyright garbage: remove false copyright detections

When no more false violations left and over-reporting reduced:

- Adress real issues if any: remove dependency with offending license
- Get the generated SBOM / NOTICE files
- Be done

...after lots of manual effort (for initial scans) which cannot be avoided, because:

1. Not all data can be gathered automatically
2. Design principle to prefer over-reporting over under-reporting is necessary

## Reducing the manual effort

1. Do just good enough compliance: Minimize violations be making the policy
   consider risk profile of the respective project scanned
2. Use global configuration: Share manually crafted fixes across projects or organizations
3. Maybe: Accept a bit of over-reporting in SBOMs for non-offending licenses
4. Increase automation of common tasks when working with ORT scans
5. Reduce feedback cycle of making fixes

## orthw

Background:

- A thin wrapper of ORT's (helper) CLI written in bash
- Goal: Speed-up working with ORT scans
- Target audiences: heavy users / compliance engineers / ORT developers
- Repository: https://github.com/oss-review-toolkit/orthw
- In use since multiple years
- Can also be useful for ORT development / debugging

Approach:

1. Introduce a scan-by-scan workflow
   - Initialize an empty directory with a scan context once
   - Further commands are run in context of that scan
   - A bit similar to Git
2. Reduce the turn-around time for seeing the effect of configuration changes  
   - Re-run evaluator and reporter locally (quick)
   - Slow stages are not re-run
3. A set of simple / easy to remeber commands to help with miscellanoues tasks
   - List file locations of detected lcenses for a package
   - Auto generate some configuration entries
   - Formatting files, sorting entries, determining and removing invalid entries
   - Locate an upstream URL for a license text given a license ID
   - Delete a scan cache entry in the database
 