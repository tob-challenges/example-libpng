# Libpng AIxCC Example Challenge Repository

*NOTICE: This is an example repository containing known vulnerabilities, 
only to be used in the preparation and testing for the AI Cyber Challenge Final Competition.*

This is not a legitimate fork or copy of libpng. Please 
refer to https://github.com/pnggroup/libpng for the actual repository.

This repository is a sample to help verify AIxCC AFC format compatibility.

The AIxCC AFC [generate-challenge-task](https://github.com/AIxCyberChallenge/example-crs-architecture/blob/main/generate-challenge-task/generate-challenge-task.sh) helper script 
may be used to take oss-fuzz-compatible repositories and create AFC-formatted challenge tasks from them. 
The script can also directly task a competitor Cyber Reasoning System (CRS) with the generated challenge tasks.

This example provides a reliable and quick crash for competitor use. Competitors are encouraged to use 
the challenge generation script on public oss-fuzz-compatible repositories for further testing. 

## What do I do with this example repository?

This repository can be used to generate two example challenge tasks: a full-scan and a delta-scan task.

These challenges are represented in their respective branches, `challenges/full-scan` and `challenges/delta-scan`.

The target refs for each challenge, along with other challenge information, can be found in `.aixcc/challenge.yaml` in each branch.

Using the `generate-challenge-task` script, these example challenges can be generated with the following:

```bash
# generate full-scan challenge task artifacts
./generate-challenge-task.sh -c <crs_url> -t "https://github.com/aixcc-finals/example-libpng" -b fdacd5a1dcff42175117d674b0fda9f8a005ae88
```

```bash
# generate delta-scan challenge task artifacts
./generate-challenge-task.sh -c <crs_url> -t "https://github.com/aixcc-finals/example-libpng" -b 0cc367aaeaac3f888f255cee5d394968996f736e -r fdacd5a1dcff42175117d674b0fda9f8a005ae88
```

Please read the generate-challenge-task documentation for full details on script usage, including local artifact generation.

## What's in this example repository?

This example contains a *very* simple addition of a crash in the `png_handle_iCCP` function. 

This bug simulates changes made by a junior programmer, wishing to update the 8-byte character header of PNG's ICCP chunk to wide characters 
to support unicode and utf-8 localizations. Done improperly, this introduces a buffer over-read and other issues.

To reproduce the crash using the provided fuzz-tooling (oss-fuzz), the `helper.py` script can be used as follows:

```bash
# build libpng fuzzers with oss-fuzz
<generated-fuzz-tooling-path>/infra/helper.py build_image --pull libpng
<generated-fuzz-tooling-path>/infra/helper.py build_fuzzers --clean libpng <generated-libpng-realpath>
<generated-fuzz-tooling-path>/infra/helper.py check_build libpng

# reproduce crash with input
<generated-fuzz-tooling-path>/infra/helper.py reproduce libpng libpng_read_fuzzer .aixcc/vulns/vuln_0/blobs/sample_data.bin
```

Note: if a delta-scan task was generated, the crash will not occur until the delta diff is applied.

Specific build and run parameters may vary depending on your host system, please follow oss-fuzz documentation if errors occur.

## Further notes about this example and the AFC

As written in the AFC rules, procedures, and scoring guides: during the competition rounds, 
a CRS will be directly tasked with challenge tasks from the AFC game infrastructure, a CRS 
will not have direct access to the GitHub repositories such as this example repository.

This repository was tested and proven compatible with the public oss-fuzz repository at the 
time of release. The compatible oss-fuzz ref at the time of this release is `946ba48ddcf4b9d9d58a7e2ff63c673873250ad7`. 
Future changes to the public repository may break compatibility. It is also confirmed to be tested and proven compatible
with oss-fuzz-aixcc ref `d5fbd68fca66e6fa4f05899170d24e572b01853d`.

It also should be noted that this example is not meant to be a comprehensive test for a CRS.

1. The challenge is not meant to reflect the quality or difficulty of the challenges in the AFC. 
2. This repository does not contain sufficient functional testing to properly 
assess the quality of patches against the example challenge.
3. This repository does not supply competitors with vulnerability discovery or patch assessment 
against the example challenge. See https://github.com/aixcc-finals/example-crs-architecture/tree/main/example-competition-server
for vulnerability discovery and patch assessment test capability.

