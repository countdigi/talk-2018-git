class: top

![TEDDY](images/teddy-logo.gif)

<br/>

### Labdata Standard 1.0

April 2017<br/>
<br/>
University of South Florida<br/>
Health Informatics Institute<br/>

---

![TEDDY](images/teddy-logo.gif)

<br/>

### Sections

- Labdata Standard 1.0
- Future Considerations

---

![TEDDY](images/teddy-logo.gif)

<br/>

## Labdata Standard 1.0

---

![TEDDY](images/teddy-logo.gif)

<br/>

### Intent

Formalize a policy and procedure for accepting subject sample omics data into
the Data Coordinating Center.

---

![TEDDY](images/teddy-logo.gif)

<br/>

### Namespace

The TEDDY labdata filesystem namespace is:

- From Linux: `/labdata/teddy/`
- From Windows: `\\epi\root\datadepot\teddy\labdata`<br/>
  (Shorcut is `U:\teddy\labdata`)

Notes:
- Read-Only except for DCCO actions (e.g. Creating Bundles and Bundle Indexes).
- Windows mount path is on an alternate axis (`teddy` precedes `labdata`).

---

![TEDDY](images/teddy-logo.gif)

<br/>

### Filesystem

Labdata uses the Oracle ZFS Filesystem as its primary storage platform.

ZFS offers one of the highest-levels of data integrity available in the industry
and is among few that actively guard against silent media corruption.
The DCCO "Integrity Scrubs" the Labdata ZFS Filesystem on a regular schedule, e.g.:

    Data Profile: Double parity, Log Profile: Mirrored log
    Online Data Errors: No known persistent errors
    Scrub Status Scrub completed: 0 errors
    2017-3-30 09:47:13 (73h40m)

See: https://en.wikipedia.org/wiki/ZFS#Data_integrity

---

### TEDDY Labdata Lifecycle

<img src="images/omics-labdata-flow.svg" style="max-height: 60%; max-width=60%;"/>

---

![TEDDY](images/teddy-logo.gif)

<br/>

### Labdata Lifecycle

1. Samples collected from Subjects
1. Samples shipped to Provider for analysis (DNA Sequencing, etc.)
1. Provider performs analysis
1. Provider transfers results via Secure FTP to their Provider Inbox
1. Provider notifies DCCA with a Transfer Report
1. DCCA reviews/verifies Transfer Report
1. DCCA generates an Ingest Request based on the Transfer Report and notifies DCCO
1. DCCO reviews/verifies the Ingest Request
1. DCCO creates a new Bundle and generates a Bundle Index
1. DCCO notifies the DCCA

---

![TEDDY](images/teddy-logo.gif)

<br/>

### Concepts

---

![TEDDY](images/teddy-logo.gif)

<br/>

#### Provider

A Provider is a named lab or similar entity which processes subject samples and transfers
the analysis results to the DCC.

Since a lab may be associated with more than one study, `teddy-` should be prefixed to its
provider account name (e.g. `teddy-abc`).

The same lab providing analysis for a different study will have a different account name
(e.g. `acme-abc`) as well as a separate Provider Inbox.

---

![TEDDY](images/teddy-logo.gif)

<br/>

#### Data Transfer Folder (DTF)

A top-level directory in the Provider Repository.

The atomic unit of transfer from a Provider is a Data Transfer Folder.

A Data Transfer Folder consists of:

- A Data Transfer Folder Name, e.g.:<br/>
  - `omics-1701`

- One or more Data Transfer Folder Paths, e.g.:
  - `Batch1/processed/XYZ123/XYZ123_1.fastq.bz2`
  - `Batch1/processed/XYZ123/XYZ123_2.fastq.bz2`

---

![TEDDY](images/teddy-logo.gif)

<br/>

#### Data Transfer Folder Example

Full Path of Provider Inbox (`/labdata/teddy/abc/_inbox/`) with the Data Transfer Folder Name `omics-1701/`:

- `/labdata/teddy/abc/_inbox/omics-1701/`

Full path of Provider Inbox (`/labdata/teddy/abc/_inbox/`), Data Transfer Folder Name (`omics-1701/`), and Data Transfer Folder Paths:

- `/labdata/teddy/abc/_inbox/omics-1701/Batch1/processed/XYZ123/XYZ123_1.fastq.bz2`

- `/labdata/teddy/abc/_inbox/omics-1701/Batch1/processed/XYZ123/XYZ123_1.fastq.bz2`

---

![TEDDY](images/teddy-logo.gif)

<br/>

#### Provider Inbox

The location where Data Transfer Folders from a Provider are transmitted.

The provider transmits each Data Transfer Folder to their `inbox` directory on DCC FTP (`dccmirror.epi.usf.edu`)
which maps internally to `/labdata/teddy/<provider_name>/_inbox/` (e.g. `/labdata/teddy/abc/_inbox/`).

The DCCA should forward the following to the DCCO to establish a DCC FTP account and Provider Inbox:

- Provider account name: (e.g. `teddy-abc`)
- Provider source IPs: (e.g. `37.34.43.100`)

Provider Accounts are documented here:<br/>
https://github.com/usf-hii/docs/blob/master/omics/labdata-filetransfer-accounts.md

---

![TEDDY](images/teddy-logo.gif)

<br/>

#### Provider Repository

The final resting place of data sent, verified, and accepted from a Provider.

Flat namespace under `/labdata/teddy/` (e.g. `/labdata/teddy/abc/`) and
contains Bundles and their corresponding Bundle Indexes.

Special directories are prefixed with the `_` character (e.g. `_inbox`) and not part of the Provider Repository
(e.g. `/labdata/teddy/abc/_inbox/`).

---

![TEDDY](images/teddy-logo.gif)

<br/>

#### Bundle

A Bundle is a specially named directory in the Provider Repository.

A Bundle is created by moving one or more Data Transfer Folders from a Provider Inbox
into the Provider Repository under the Bundle directory establishing a uniquely
namespaced, immutable set of data at a particular point in time.

Once a Bundle is created it can never be altered and a new Bundle must be created for ingest of additional files.

This immutability establishes provenance and simplifies verification, replication, and archiving operations.

---

![TEDDY](images/teddy-logo.gif)

<br/>

#### Bundle Name

A Bundle's name is composed of a Bundle Prefix (e.g. `Stool-16S`), a `-` character, and Bundle Prefix Number (e.g. `1`).

The Bundle Prefix Number is incremented for each new Bundle created using the same Prefix (e.g. `Stool-16S-1`, `Stool-16S-2`).

As an example, within the Provider Repository `/labdata/teddy/abc/`, the full paths of 3 Bundles might be:

- `/labdata/teddy/abc/Stool-16S-1/`
- `/labdata/teddy/abc/Stool-16S-2/`
- `/labdata/teddy/abc/Plasma-WGS-1/`

---

![TEDDY](images/teddy-logo.gif)

<br/>

#### Bundle Index

For every Bundle, a corresponding file called a Bundle Index is generated by the DCCO at Bundle creation
and located beside the bundle as `<bundle_name>.index` in the Provider Repository.

As an example, within the Provider Repository `/labdata/teddy/abc/`, the  Bundle and Bundle Index would be:
- `/labdata/teddy/abc/Stool-16S-1/`
- `/labdata/teddy/abc/Stool-16S-1.index`

---

![TEDDY](images/teddy-logo.gif)

<br/>

#### Bundle Index Format / Checksum Field

The Bundle Index enumerates each file stored in the Bundle on its own colon-separated line consisting of the fields:
1. Data Transfer Folder Name and Data Transfer Folder Path<br/>
   (`omics-1710/Batch1/processed/XYZ123/XYZ123_1.fastq.bz2`)
2. Size in bytes<br/>
   (`89936774`)
3. Checksum Values<br/>
   (`[md5]8e787a[sha256]613e54`)

---

![TEDDY](images/teddy-logo.gif)

<br/>


#### Bundle Index Format Checksum

Field 3 of a line in the Bundle Index contains all checksums.

Current checksums supported are `md5` and `sha256`.  Each checksum is prefixed with `[checksum_type]` and its value.

For example, the checksum field for a file with an `md5` checksum and a `sha256` checksum:<br/>
`[md5]8e787a[sha256]613e54`

Extracting an md5 value in Bash:<br/>
`awk -F: '{print $3}' /labdata/teddy/abc/Stool-16S-1.index \ `<br/>
`  | sed -r 's/\[md5\]([^\[]*).*/\1/'`<br/>

*Note*: The `md5` and `sha256` checksum values are truncated for readability.

---

![TEDDY](images/teddy-logo.gif)

<br/>

#### Bundle Index Format Example

For example, within the Provider Repository `/labdata/teddy/abc`, the Bundle `Stool-16S-1` containing the
Data Transfer Folder `omics-1701` would have the following format:

    $ head -n2 /labdata/teddy/abc/Stool-16S-1.index

    omics-1701/Batch1/processed/XYZ123/XYZ123_1.fastq.bz2:89936774:[md5]8e787a[sha256]613e54
    omics-1701/Batch1/processed/XYZ123/XYZ123_2.fastq.bz2:86621121:[md5]f99fe7[sha256]1b5dc6

---

![TEDDY](images/teddy-logo.gif)

<br/>

### DCC Roles

Description of the Data Coordinating Center Roles:

- DCC Administration (DCCA) - Interfaces with Providers and DCCO

- DCC Operations (DCCO) - Interfaces with DCCA and performs operatioons on Data Transfer Folders included in an Ingest Request

---

![TEDDY](images/teddy-logo.gif)

<br/>

### DCC Roles @ USF HII

- DCC Administration (DCCA)
  - Primary Contact: Dena Garcia, Data Engineering

- DCC Operations (DCCO)
  - Primary Contact: Kevin Counts, Systems Engineering

Ingest Requests updated at:<br/>
https://github.com/usf-hii/docs/blob/master/omics/labdata.md

---

![TEDDY](images/teddy-logo.gif)

<br/>

## Part II - Future Considerations

- Transfer Report
- Provider Contracts
- Software Automation

---

![TEDDY](images/teddy-logo.gif)

<br/>

### Transfer Report

- Transfer Reports are currently a free-form e-mail between Provider and DCCA.

- A more formalized standard for Transfer Reports could be beneficial (`HdX LabCli` has this idea)

- Transfer Report could supplement/substitute Data Transfer Folder Path naming standards if all necessary
  metadata was associated with each Data Transfer Folder Path

---

![TEDDY](images/teddy-logo.gif)

<br/>

### Provider Contracts

New Provider Contracts should ideally:
- Agree on a shipment schedule (e.g. results should be shipped N days after processing for early feedback of pipeline)
- Agree on a file checksum requirement
  - Suggestion: `<file>.md5sum` for each file transmitted (e.g. `foo/baz/bar.txt` and `foo/baz/bar.txt.md5sum`)
- Document a Data Transfer Folder Path standard for results data
  - A General standard would be helpful (a. No Spaces, b. Only use the set [`a-z`, `A-Z`, `0-9`, `-`, `_`, or `.`] for file and directory
    names)
  - Specific Provider/Analyte may require altering or adding additional requirements to General standard including domain-specific file and directory naming convention
  - Matching a file to a Sample ID is sometimes challenging without this Specific Provider/Analyte standard and is difficult to
    automate

---

![TEDDY](images/teddy-logo.gif)

<br/>

### Software Automation

Although humans perform most of the DCCO and DCCA actions,
automating more of these tasks may be beneficial in the future.

For Example:<br/>

[HdX LabCLI](https://github.com/usf-hii/dataeng-hdx-labcli) -
Python Command-Line Application for sample processing labs to transfer files to the DCC using the
[HdExchange](https://github.com/usf-hii/dataeng-hdexchange) Web API.

---

![TEDDY](images/teddy-logo.gif)

<br/>

## Thank you!

Standard at: <br/>
https://github.com/usf-hii/docs/blob/master/omics/labdata-standard.md
