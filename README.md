# Jethro Pastoral Ministry Manager

> **This is a personal fork of [tbar0970/jethro-pmm](https://github.com/tbar0970/jethro-pmm)** maintained by [@sevasek](https://github.com/sevasek).
> It stays in sync with upstream and adds targeted enhancements for real-world migration scenarios.
> For the canonical release, see the [upstream project](https://github.com/tbar0970/jethro-pmm) or the hosted service at [Easy Jethro](https://easyjethro.com.au).

---

Jethro Pastoral Ministry Manager is a web-based tool which helps churches keep track of people, families, groups, attendance, pastoral tasks, church services, rosters and documents.  Jethro doesn't force you to work in a particular way but gives you flexible, lightweight tools to support your own style of ministry.

The Jethro software is free and open source (GPL) and runs on a standard [LAMP](https://en.wikipedia.org/wiki/LAMP_%28software_bundle%29) web server.  Jethro's real advantages come to the fore when it's running on a proper web server, but it can also be run on a single PC using [XAMPP](XAMPP).

Jethro PMM is the software that powers online services such as [Easy Jethro](https://easyjethro.com.au) who also offer a [demo system](https://easyjethro.com.au/demo/).

---

## Enhancements in This Fork

### CSV Import: Person ID Matching
- If your CSV includes an `id` or `person_id` column, Jethro will look up people by their exact database ID first — skipping name-based matching entirely.
- Falls back to the original name-based matching when no ID column is present, so existing imports are unaffected.
- Solves import failures caused by multiple archived duplicates (e.g. during migrations from Fluro).
- The import UI checkbox now clearly explains the matching strategy.

---

# Download and Install

Download the latest version of Jethro from the [releases page](https://github.com/tbar0970/jethro-pmm/releases)

System requirements:
* Some kind of web server (apache/nginx etc)
* MySQL 8.0 or above
    * with [ONLY_FULL_GROUP_BY](https://dev.mysql.com/doc/refman/8.4/en/sql-mode.html#sqlmode_only_full_group_by) disabled
* PHP 8.1 or above
    * with [gettext extension](https://www.php.net/manual/en/book.gettext.php) enabled
    * with [zip extension](https://www.php.net/manual/en/book.zip.php) enabled
    * with [xmlwriter extension](https://www.php.net/manual/en/book.xmlwriter.php) enabled, to create DOCX files
    * [GD library](https://www.php.net/manual/en/book.image.php) recommended, to manage the size of uploaded photos
    * with [curl extension](https://www.php.net/manual/en/book.curl.php) enabled, if you intend to use the Mailchimp integration
    * with [exif extension](https://www.php.net/manual/en/book.exif.php) enabled, if you would like to automatically rotate images

Installation steps:
1. Unzip the files into a web-accessible folder on your web server
2. Create a MySQL database and database user. If asked, choose utf8_unicode_ci as the character set and collation.
3. Edit `conf.php` (copy from `conf.php.sample`) and fill in the system name, URL, and database details.
4. Open the Jethro URL in your browser — the installer starts automatically and prompts you to create an admin account.

You might like to download and load some [sample data](https://easyjethro.com.au/demo/jethro_demodata.gz) (SQL that can be loaded into MySQL; see [here](https://github.com/tbar0970/jethro-pmm/pull/1398) for instructions).

---

# Features

| Area | Capabilities |
|---|---|
| **People & Families** | Directory, photos, custom fields, vCard export, CSV import/export |
| **Congregations** | Multiple congregations, membership status, statistics |
| **Groups** | Bible studies, volunteers, ministries, membership statuses, group picker |
| **Attendance** | Weekly recording by congregation or group, display, stats, check-ins |
| **Notes / Pastoral Tasks** | Free-form notes, templates, action plans, assigned tasks |
| **Rosters** | Role definitions, roster views, availability tracking, automated reminders |
| **Service Planning** | Run sheets, component library (songs, prayers, liturgy), reporting |
| **Documents** | File storage per person/family |
| **Reports** | Rule-based person queries, scheduled/email reports |
| **Admin** | User accounts, permissions, custom fields, Mailchimp sync, SMS, upgrades |

---

# Documentation

## User Documentation

Documentation articles are hosted at [Easy Jethro Support](https://easyjethro.com.au/support/).

## Developer Documentation

See [`docs/developer_tips.md`](docs/developer_tips.md) for coding conventions and helper function notes.

---

# Support and Discussion

- **Bug reports:** [Open an issue](https://github.com/tbar0970/jethro-pmm/issues/new) on the upstream tracker
- **Feature requests:** [Check existing requests](https://github.com/tbar0970/jethro-pmm/issues?q=is%3Aopen+is%3Aissue+label%3Afeature-request) before opening a new one
- **Real-time support:** Consider [Easy Jethro](https://easyjethro.com.au) for hosted support

---

# 🚀 Roadmap & Future Releases

These are ideas being considered for contribution back to upstream or development in this fork.

### 1. Data Migration Toolkit (Import Profiles)
Many churches switching from services like Fluro, Planning Center, or Breeze need to remap column names, handle duplicate archived records, and match on external IDs.  The goal is a set of named migration profiles in the CSV importer: select your source system, and the importer pre-maps known column names and applies the correct matching strategy.  **Priority: High** — this fork already has Person ID matching as the first building block.

### 2. Attendance Trend Dashboard
A visual summary panel on the home screen showing weekly/monthly attendance trends across congregations and groups — sparklines or a simple bar chart built with the data already captured in `view_6_attendance`.  Helps pastoral teams spot engagement drops early without needing to run custom reports.  **Priority: Medium** — adds meaningful value with no new data model changes.

### 3. Roster Reminder Self-Service Portal
Currently roster reminders are sent via email/SMS scripts.  A small read-only portal (shareable link, no login required) where rostered people can confirm or decline their slot and nominate an available substitute would close the loop without requiring users to log in to Jethro.  Eliminates the back-and-forth email thread that most roster coordinators experience.  **Priority: Medium-High** — directly reduces coordinator workload.

---

# Data Model

* A **person** has a name and various other properties.
* Every person belongs to exactly one **family** — a collection of persons at the same address.
* Every person belongs to exactly one **congregation** — the main grouping within a church.  (Persons with status 'contact' can be congregation-less.)
* A person can belong to several **groups** representing bible studies, volunteers, training completions, welcoming processes, etc.
* A person's **attendance** at their congregation or a group can be recorded week by week.
* **Notes** can be added to persons or families — free-form information or tasks assigned to a Jethro user.
* A **report** shows persons matching certain rules about personal details, group memberships, etc.
* A **service** is when a congregation meets on a particular date, with topic, bible passages, and a run sheet of **service components** (songs, prayers, etc.).
* A **roster role** is a role to be played in a service (e.g. reading the bible).  A **roster view** groups related roles; a **roster assignment** assigns a person to a role for a specific service.

An extensive feature list is on the [Easy Jethro site](https://easyjethro.com.au/#features).

---

# Naming

Jethro is designed to facilitate delegation and team ministry.  Its name comes from Exodus 18:13–23 where Moses' father-in-law Jethro introduces him to the skill of delegation.

---

# Acknowledgements

Jethro development has been sponsored or contributed to by several churches worldwide:
* [Christ Church Inner West Anglican Community](http://cciw.org.au), Sydney, Australia (founding sponsor)
* [Redlands Presbyterian Church](https://redlands.org.au/), Queensland, Australia (service planning features)
* [St Peter's Woolton](https://www.stpeters-woolton.org.uk), Liverpool, UK (date field and photo features)
* [Coast Evangelical Church](https://www.coastec.net.au), Forster, Australia (group-membership statuses, attendance enhancements)
* [St George North Anglican Church](https://www.snac.org.au), Sydney, Australia (vCard export)
* [Macquarie Anglican Church](http://www.macquarieanglican.org/), Sydney, Australia (note-search and SMS-family feature)
* [Dalby Presbyterian Church](http://www.dpc.cc/), Queensland, Australia (edit/delete note features and family photos)
* [Professional Standards Unit](https://safeministry.org.au), Anglican Diocese of Sydney (custom fields etc)

There are also several GitHub contributors whose input is invaluable.
