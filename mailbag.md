---
layout: page
title: Mailbag Specification
permalink: /spec/
nav_order: 2
has_children: true
---

# Mailbag Specification (1.0)

This is a display version of the specification, which is managed and versioned with markdown in a [Github repository](https://github.com/UAlbanyArchives/mailbag-specification).

## Contributors

* Alison Rhonemus
* Dave Mayo
* Gregory Wiedeman
* Hannah Wang
* Hillel Arnold
* Jessica Smith
* Joshua Westgard
* Katherine Martinez
* Mark Wolfe
* Mat Kelly
* Nathan Tallman
* Santhilata Venkata
* Walker Sampson
* Winnie Schwaid-Lindner


## 1. Introduction


### 1.1 Purpose

No single email format satisfies all needs for long-term preservation. To ensure a wide variety of future use cases, email should be preserved using multiple formats. The purpose of the Mailbag Specification is to define a set of file layout conventions to allow for the preservation of multiple representations of email using different file formats. A “mailbag” allows these multiple representations to be preserved in a consistent and structured package that may be created and acted upon by computer software.

Importantly, a mailbag contains multiple representations of email, rather than multiple versions. For example, a mailbag may contain an email message using EML, PDF, and WARC formats, but the content of the message will be the same across all formats. This is useful since each format has different affordances for email preservation. An EML representation is a common open format for preserving the structure of an email message. A PDF may best preserve the visual representation of an email message as a document and preserves images hosted externally. A WARC file may be useful to preserve any externally-hosted content, as well as any CSS interactivity. If a user wishes to manage multiple versions of an email message, such as for managing redactions or restrictions, they should use multiple mailbags, each containing the representations needed to best meet their use cases.

The Mailbag Specification is an extension of [RFC 8493](https://tools.ietf.org/html/rfc8493), The BagIt File Packaging Format, hereafter referred to as Bagit. All mailbags MUST conform to RFC 8493 as valid “bags.” The extension of the Bagit specification allows for the use of existing Bagit implementations and workflows while also utilizing Bagit’s strong integrity assurances through its use of checksum algorithms. Just like the Bagit Specification, the Mailbag Specification is designed to provide just enough structure to accomplish its purpose while also enabling further extension to meet local practices.


### 1.2 Status of this Document

Mailbag Specification 1.0


### 1.3 License

[CC0 - “No Rights Reserved”](https://creativecommons.org/share-your-work/public-domain/cc0/) !["CC0 Public Domain logo"](https://i.creativecommons.org/p/zero/1.0/88x31.png)


### 1.4 Requirements

This document is designed as an extension of The BagIt File Packaging Format v1.0 [[RFC8493](https://tools.ietf.org/html/rfc8493)]. and it is recommended that all mailbags should comply with that specification. However, since [bagit-python](https://github.com/LibraryOfCongress/bagit-python) should be a common method of creating mailbags and bagit-python is currently only tested to support v0.97, and v1.0 support is unclear, mailbags conforming to that version are also valid.

The key words “MAY”, “MUST”, “MUST NOT” ,“RECOMMENDED”, “REQUIRED”, “OPTIONAL”, “SHOULD”, and “SHOULD NOT” in this document are to be interpreted as described in BCP 14 [[RFC2119](https://tools.ietf.org/html/rfc2119)] [[RFC8174](https://tools.ietf.org/html/rfc8174)] when, and only when, they appear in all capitals, as shown here.


### 1.5 Terminology

This document relies on the terminology defined in the [Bagit specification section 1.3](https://tools.ietf.org/html/rfc8493#section-1.3).

The following additional terms have precise definitions as used in this document:


**source:** The original capture method or format of email prior to the creation of a mailbag. This could be the IMAP protocol, a proprietary API, or file formats such as MBOX, EML, PST, etc. It is important to note that the source may not be the email account’s original form, but only the format that existed prior to packaging as a mailbag.


**derivative:** An additional email format created from the email source during, or prior to, the creation of a mailbag. For example, a mailbag implementation may create multiple derivative formats for email, such as EML, PDF, or WARC during the creation of a mailbag.


**email folder:** A grouping of email created by an email account user to arrange and manage email. Inbox, Sent Mail, Drafts, and Trash are common default email folders, and email users also commonly create custom folders. Email folders can contain other email folders. The structure of email folders is included in PST exports, but is not fully represented in other export formats. A message’s folder also may or may not be included in its headers. The X-Folder header or the X-Gmail-Labels header in Gmail exports are common uses.


**email format:** A file format used for storing email on a computer filesystem. Common email formats are MBOX, EML, PST, or MSG, and email may also be stored using PDF, WARC, or other file formats.


**format subdirectories:** A set of optional directories in the payload of a mailbag. Format subdirectories are named using the lower case file extension of the email format they contain. For example, mailbags are expected to often contain “mbox,” “eml,” “pdf,” and/or, “warc” as format subdirectories.


**Message-ID:** An identifier for email messages generated by the client program sending the email. Message-ID is included in email headers and typically available in email formats such as MBOX, PST, EML, and MSG.


**Mailbag-Message-ID:** An identifier assigned to each email message during the creation of a mailbag. Mailbag-Message-IDs are unique within a mailbag and must be valid filenames in both Unix-based and Windows operating systems. Thus, they cannot contain characters such as `< > : " / \ | ? *` and are RECOMMENDED to be 36 characters or less.


## 2. Overview of a Mailbag

A mailbag is a set of directories and files with a common structure defined by this document and the [Bagit specification](https://tools.ietf.org/html/rfc8493). Since a number of formats have different affordances for email preservation, a mailbag is used to manage email exports by creating and connecting multiple derivative formats.

A mailbag MUST be a valid Bagit bag with these additional requirements:

1. The payload directory of a mailbag MUST contain at least one of a set of format subdirectories named after each of the email format it contains in lower case.
2. A mailbag MUST contain a bag-info.txt file containing a number of required and optional fields.
3. A mailbag MUST contain an additional tag file named “mailbag.csv”. This file MUST be a valid Comma-separated values (CSV) file, MUST list each message with an identifier and MUST include the information necessary to connect different representations of email included in a mailbag.
4. A mailbag MUST contain a Bagit tag manifest file.


A mailbag MAY also contain a few optional elements:

1. The payload directory of a mailbag MAY contain a subdirectory labeled “attachments” which contains files attached to email messages.
2. A mailbag may contain two additional tag files, named “folders_not_retained.txt” and “messages_not_retained.txt”, used to document email folders and messages not retained prior to packaging into a mailbag.


### 2.1 Example of a mailbag

	<base directory>/
         |
         +-- bagit.txt
         |
         +-- bag-info.txt
         |
         +-- mailbag.csv
         |
         +-- manifest-<algorithm>.txt
         |
         +-- tagmanifest-<algorithm>.txt
         |
         +-- data/
              |
              +-- mbox/
              |     +-- [payload files]
              +-- pdf/
              |     +-- [payload files]
              +-- warc/
              |     +-- [payload files]
              +-- attachments/
                    +-- [Mailbag-Message-ID]/
                    |     -- attachments.csv
                    |     -- [payload files]
                    +-- [Mailbag-Message-ID]/
                          -- attachments.csv
                          -- [payload files]


## 3. Identifiers

Mailbags use identifiers to maintain connections between individual email messages in different formats and filesystem locations. The commonly-used identifier for email messages is Message-ID as described in [RFC2392](https://tools.ietf.org/html/rfc2392). However, there are cases when the Message-ID for a message is not present and Message-IDs also commonly contain characters that are incompatible with some filesystems. Thus, mailbags contain an additional Mailbag-Message-ID for each message that is unique within a mailbag. They MUST be unique regardless of case, as "MSG-1" would be invalid if "msg-1" was already used. Mailbag-Message-IDs are REQUIRED for each message and must be valid as filenames in both Unix-based and Windows operating systems. For example, they cannot contain characters such as:

    <  >  :  "  /  \  |  ?  *

Sequential numbers MAY be used as Mailbag-Message-IDs. It is RECOMMENDED for Mailbag-Message-IDs to be at most 36 characters long to avoid file path character limits. Implementations can map Mailbag-Message-IDs to Message-IDs using the mailbag.csv tag file as defined in [Section 5.3](#53-mailbagcsv).


## 4. Payload Structure

The payload directory of a mailbag MUST contain format subdirectories named after each of the email formats it contains in lower case. At least one format subdirectory is required.


### 4.1 Format subdirectories

A mailbag payload MUST contain at least one subdirectory named from the following set:

* mbox
* pst
* msg
* eml
* pdf
* warc

If present, each of these format subdirectories MUST contain email in the format designated by the name of the subdirectory. Format subdirectories MUST be in lower case and may contain either email in the source format or derivative formats. One payload subdirectory MAY be designated as the source format in a REQUIRED Mailbag-Source field in the bag-info.txt tag file. The format subdirectory for messages in the WARC format MUST be named “warc” even if the use of compression is denoted in the file extension, such as with “.warc.gz.”

Additional, "companion" files outsile of the email formats designated by the folder name MAY also be present in the source format subdirectory. It is RECOMMENDED that companion files be limted to text-based metadata files, and for a clear association between companion files and the email formats they describe via a filenaming convention or bag-into.txt metadata.


#### 4.2.1 Example of format subdirectories

	<base directory>/
         |
         +-- bagit.txt
         |
         +-- bag-info.txt
         |
         +-- mailbag.csv
         |
         +-- manifest-<algorithm>.txt
         |
         +-- tagmanifest-<algorithm>.txt
         |
         +-- data/
              |
              +-- mbox/
              |     +-- [payload files]
              +-- eml/
              |     +-- [payload files]
              +-- pdf/
              |     +-- [payload files]
              +-- warc/
                    +-- [payload files]


### 4.2 Additional subdirectories within format subdirectories

Format subdirectories MAY contain additional subdirectories that represent the folder structure of the source email account. It is RECOMMENDED to use additional subdirectories when an email account uses folders meaningfully to preserve the original arrangement of the email account. This MAY include the directory structure of export files being packaged. This MAY be joined with directory structures contained within .pst files or email folders extracted from X-Folder, X-Gmail-Labels, or other email header fields. The Derivatives-Path field within the mailbag.csv tag file, as described in [Section 5.3.2](#532-mailbagcsv-columns) MUST document any additional subdirectories.

A mailbag may not contain every email folder originally present in the email account. In this case, it is RECOMMENDED to document which folders were not retained in a tag file named “folders_not_retained.txt”, as defined in [Section 5.4.2](#542-example-folders_not_retainedtxt).


#### 4.2.1 Example of format subdirectories with additional subdirectories

	<base directory>/
         |
         +-- bagit.txt
         |
         +-- bag-info.txt
         |
         +-- mailbag.csv
         |
         +-- manifest-<algorithm>.txt
         |
         +-- tagmanifest-<algorithm>.txt
         |
         +-- data/
              |
              +-- mbox/
              |     +-- All mail Including Spam and Trash.mbox
              +-- eml/
              |     +-- Inbox
              |     |     +-- [payload files]
              |     +-- Sent Mail
              |     |     +-- [payload files]
              |     +-- Junk
              |           +-- [payload files]
              +-- pdf/
              |     +-- Inbox
              |     |     +-- [payload files]
              |     +-- Sent Mail
              |     |     +-- [payload files]
              |     +-- Junk
              |           +-- [payload files]
              +-- warc/
                    +-- Inbox
                    |     +-- [payload files]
                    +-- Sent Mail
                    |     +-- [payload files]
                    +-- Junk
                          +-- [payload files]


#### 4.2.2 Additional Subdirectories and Filesystem Incompatibility

Additional Subdirectories may include paths extracted from .pst files or email headers. Thus they may contain characters that cannot be written as  directory names in the local filesystem. Mailbags manage this using the Original-File, Message-Path, and Derivatives-Path fields contained in the mailbag.csv tag file, as described in [Section 5.3.2](#532-mailbagcsv-columns). The Message-Path MUST contain the string as read from a .pst file or email header and the Derivatives-Path field MUST be escaped and be able to be written to directories on the filesystem that packaged the mailbag. A user can compare these fields to recreate any intellectual arrangements with escaped directory paths.

Mailbag implementations SHOULD also follow the interoperability recommendations in [Bagit specification section 6.1.1.3](https://tools.ietf.org/html/rfc8493#section-6.1.1.3).


### 4.3 Attachment Subdirectory

A mailbag payload directory MAY contain a subdirectory labeled “attachments”. The Attachment Subdirectory OPTIONALLY contains attachment files extracted from email messages. It is RECOMMENDED to extract attachments and place them in the attachment subdirectory when creating derivative formats that do not contain embedded attachments, such as PDF derivatives.

If present, the Attachment Subdirectory MUST contain additional subdirectories named with the Mailbag-Message-ID of the email where the contained attachments were extracted from. If present, these Mailbag-Message-ID subdirectories SHOULD contain files attached to the corresponding email message.

Additionally, the attachment Mailbag-Message-ID subdirectories MUST contain a CSV file named "attachments.csv" containing a line for each attachment. These CSV files MUST comply with CSV rules described in [Section 5.5](#55-csv-tag-files).

#### 4.3.1 attachments.csv column headers 

Each attachments.csv file MUST contain the following column headers in the order that they are listed:

 * Original-Filename
 * Mailbag-Filename
 * MimeType
 * Content-ID
 
#### 4.3.2 attachments.csv columns 

**Original-Filename**:

 * Denotes the original filename for the attachment. If the original filename for the attachment is unknown, this field should be "unknown".

**Mailbag-Filename**:

 * Denotes the filename used to write the attachment within the mailbag. This SHOULD be the same as Original-Filename, except for cases of missing filenames or interoperability issues as described in [Section 4.4.3](#443-attachment-filename-interoperability-issues).

**MimeType**:

* The MIME type for the attachment. For cases where the MimeType is unknown, it is RECOMMENDED to use an empty string.

**Content-ID**:
 
* The ContentID for the attachment listed in the source email. For cases where the ContentID is unknown, it is RECOMMENDED to use an empty string.

 
#### 4.3.3 Example attachment subdirectory

	<base directory>/
         |
         +-- bagit.txt
         |
         +-- bag-info.txt
         |
         +-- mailbag.csv
         |
         +-- manifest-<algorithm>.txt
         |
         +-- tagmanifest-<algorithm>.txt
         |
         +-- data/
              |
              +-- mbox/
              |     +-- [payload files]
              +-- eml/
              |     +-- [payload files]
              +-- pdf/
              |     +-- [payload files]
              +-- warc/
              |     +-- [payload files]
              +-- attachments/
                     +-- [Mailbag-Message-ID]/
                     |    -- attachments.csv
                     |	  -- [payload files]
                     +-- [Mailbag-Message-ID]/
                     |    -- attachments.csv
                     |	  -- [payload files]
                     ...


#### 4.3.4 Links as attachments

Email messages may contain URL links as attachments. Links as attachments SHOULD be serialized as .URL files. An attached link MAY also be stored as a WARC file in addition to a URL file.

	<base directory>/
         |
         +-- bagit.txt
         |
         +-- bag-info.txt
         |
         +-- mailbag.csv
         |
         +-- manifest-<algorithm>.txt
         |
         +-- tagmanifest-<algorithm>.txt
         |
         +-- data/
              |
              +-- mbox/
              |     +-- [payload files]
              +-- eml/
              |     +-- [payload files]
              +-- pdf/
              |     +-- [payload files]
              +-- warc/
              |     +-- [payload files]
              +-- attachments/
                    +-- [Mailbag-Message-ID]/
                    |    -- attachments.csv
                    |    -- link.url
                    |    -- link.warc
                    +-- [Mailbag-Message-ID]/
                    |    -- attachments.csv
                    |    -- meeting.ics
                    ...


### 4.4 Payload Filenames


#### 4.4.1 Message Filenames

For source formats, it is RECOMMENDED to maintain the original filenames for both account-level or message-level files. For derivative formats, filenames MUST be the email’s Mailbag-Message-ID.


##### 4.4.1.1 Account-level formats (MBOX, PST, IMAP)

When account-level email formats, such as MBOX or PST, are the source format, it is RECOMMENDED to maintain the original file name for these files. There will be no source file present when email is packaged using the IMAP protocol. When an account-level derivative, such as MBOX or PST, is created when packaging using the IMAP protocol, these filenames MUST be the mailbag’s External-Identifier as listed in bag-info.txt.


##### 4.4.1.2 Message-level formats (EML, MSG, PDF, WARC)

When the email source is a set of message-level formats, such as EML, MSG, or PDF, it is RECOMMENDED to maintain the original filenames for these files. Derivative formats for message-level email sources still MUST use the file’s Mailbag-Message-ID with a new file extension to reflect the change in format.

When the email source uses the IMAP protocol or account-level formats such as MBOX or PST, there will be no message-level original filenames, For these cases, message-level derivative files MUST be named using each message’s Mailbag-Message-ID.

File extensions for WARC files MAY denote if compression is used, such as the .warc.gz extension. 


#### 4.4.2 Attachment Filenames

It is RECOMMENDED to maintain the original filenames for attachments.


#### 4.4.3 Attachment Filename Interoperability issues

Attachment filenames provide the potential for interoperability issues, as creating a Mailbag in a Windows environment may attempt to handle filenames that were originally created in a POSIX environment. When any of the original filenames for a message are invalid, they MUST be renamed using the Mailbag-Message-ID followed by a hyphen (-) and an incremental integer. Filename changes will be documented in the associated attachments.csv file as described in [Section 4.3.1](432-attachmentscsv-columns).

Mailbag implementations SHOULD also follow the interoperability recommendations in [Bagit specification section 6.1.1.3](https://tools.ietf.org/html/rfc8493#section-6.1.1.3).


#### 4.4.4 Filename Example with MBOX as source

         <base directory>/
         |
         +-- bagit.txt
         |
         +-- bag-info.txt
         |
         +-- mailbag.csv
         |
         +-- manifest-<algorithm>.txt
         |
         +-- tagmanifest-<algorithm>.txt
         |
         +-- data/
              |
              +-- mbox/
              |     -- All mail Including Spam and Trash.mbox
              +-- pdf/
              |     -- 1.pdf
              |     -- 2.pdf
              |     -- 3.pdf
              |    	...
              +-- warc/
              |     -- 1.warc.gz
              |     -- 2.warc.gz
              |     -- 3.warc.gz
              |     ...
              +-- attachments/
                     +-- 1/
                     |    -- attachments.csv
                     |    -- Image1.jpg
                     |    -- Image2.jpg
                     +-- 3/
                          -- attachments.csv
                          -- packageList.pdf
                          -- draft_for_review.odt
                          -- 3-2.odt


#### 4.4.5 Filename Example with EML as source

         <base directory>/
         |
         +-- bagit.txt
         |
         +-- bag-info.txt
         |
         +-- mailbag.csv
         |
         +-- manifest-<algorithm>.txt
         |
         +-- tagmanifest-<algorithm>.txt
         |
         +-- data/
              |
              +-- eml/
              |     -- A free and open internet.eml
              |     -- A simple pledge.eml
              |     -- A year of organizing.eml
              |     ...
              +-- pdf/
              |     -- 1.pdf
              |     -- 2.pdf
              |     -- 3.pdf
              |    	...
              +-- warc/
              |     -- 1.warc
              |     -- 2.warc
              |     -- 3.warc
              |   	...
              +-- attachments/
                    +-- 1/
                    |	-- attachments.csv
                    |	-- Image1.jpg
                    |	-- Image2.jpg
                    +-- 3/
                        -- attachments.csv
                        -- packageList.pdf


This example maintains the original filenames for message-level source formats per [Section 4.4.1.2](#4412-message-level-formats-eml-msg-pdf-warc). This can be mapped to the Mailbag-Message-ID using the Original-File field in the mailbag.csv tag file.


## 5. Tag Files

A mailbag MUST contain the required Bagit tag files as specified in [Bagit 2.1](https://tools.ietf.org/html/rfc8493#section-2.1), including a bagit.txt bag declaration and a payload manifest. In addition to the Bagit minimum requirements, a mailbag MUST contain a bag-info.txt metadata file as specified in [Bagit 2.2.2](https://datatracker.ietf.org/doc/html/rfc8493#section-2.2.2), a tag manifest file as specified in [Bagit 2.2.1](https://tools.ietf.org/html/rfc8493#section-2.2.1), and an additional mailbag.csv comma separated values (CSV) file containing identifiers and a set of commonly used email headers for each message.

Mailbag tag files MUST be UTF-8 encoded and SHOULD NOT contain a byte order mark (BOM). Tag files defined by the Bagit Specification MUST use a line feed character (LF or \n) for line endings. The mailbag.csv tag file defined in [Section 5.3](#53-mailbagcsv) MUST use carriage return and line feed characters (CRLF or \r\n). This inconsistency is necessary to meet both common practices for Bagit tag files and CSV files.

A mailbag MAY also contain two additional tag files, named “folders_not_retained.txt” and “messages_not_retained.txt” which, if present, MUST document email folders and messages that were not retained prior to, or during, packaging, and thus are not present in the payload directory. A mailbag MAY also contain additional tag files that are not defined by this document as specified in [Bagit 2.2.4](https://tools.ietf.org/html/rfc8493#section-2.2.4).


### 5.1 Example of tag files in a mailbag

	<base directory>/
         |
         +-- bagit.txt
         |
         +-- bag-info.txt
         |
         +-- mailbag.csv
         |
         +-- manifest-<algorithm>.txt
         |
         +-- tagmanifest-<algorithm>.txt
         |
         +-- folders_not_retained.txt
         |
         +-- messages_not_retained.txt
         |
         +-- data/
			+-- [payload files]
			


### 5.2 Bag-info.txt

As valid bags, mailbags retain the rules and reserved elements as specified in [Bagit 2.2.2](https://tools.ietf.org/html/rfc8493#section-2.2.2). A mailbag MUST contain the set of required Reserved Metadata Fields listed below, including the “Bagging-Date” and “External-Identifier’' fields that are reserved in [Bagit 2.2.2](https://tools.ietf.org/html/rfc8493#section-2.2.2). A mailbag MAY also contain any fields in the following set of Optional Metadata Fields. Possible values are quoted and listed in brackets. These values are case sensitive and should be included exactly as they appear. While the ordering of metadata elements MUST be preserved in compliance with [Bagit 2.2.2](https://datatracker.ietf.org/doc/html/rfc8493#section-2.2.2), Mailbags do not expect the order of bag-into.txt fields to be significant. All of both the Required Metadata Fields and the Optional Metadata Fields MUST NOT be repeated.

Bag-info.txt metadata is extensible in accordance with [Bagit 2.2.2](https://tools.ietf.org/html/rfc8493#section-2.2.2) and custom fields MAY be used. It is RECOMMENDED to use bag-info.txt to describe the email account(s) preserved within a mailbag.

#### 5.2.1 PREMIS Mapping

A mailbag is not expected to contain a full [PREMIS](https://www.loc.gov/standards/premis/) implementation, but does attempt to document information that can be mapped to PREMIS fields. A mailbag expects to be mapped to two PREMIS events, one for the initial capture of email and another for its packaging into a mailbag.


#### 5.2.2 Required Metadata Fields

**Bag-Type:** “Mailbag”

* Denotes that a bag is a mailbag.

**Mailbag-Source:** [“imap”, “mbox”, “eml”, “pst”, “pdf”, “warc”]

 * Denotes the protocol or format from which the email was packaged into a mailbag.
 
**Mailbag-Specification-Version:**

 * Denotes which version of the Mailbag Specification a mailbag is in compliance with.

**Original-Included:** [“True”, “False”]

 * Denotes whether the original source format, such as an MBOX or set of EML files are included in original form. If an included MBOX file does not contain the same content as the original, such as if messages or email folders were excluded, the value MUST be “false”. Similarly if the email was packaged using the IMAP protocol, the value MUST be “false”. It is important to note that this does not refer to the email prior to capture, but the state of the email prior to packaging. Thus, if email was modified during its previous capture into an MBOX file, the value for this field would still be “True” so long as the unmodified MBOX file was maintained while it was later packaged into a mailbag.

**Bagging-Timestamp:**

 * Timestamp denoting when a mailbag was packaged. MUST conform to [RFC3339](https://tools.ietf.org/html/rfc3339), such as 1937-01-01T12:00:27.87+00:20. Mapped to PREMIS package event, eventDateTime field.

**Bagging-Date:**

 * Date (YYYY-MM-DD) denoting when a mailbag was packaged. Less-precise duplicate of Bagging-Timestamp for compatibility with Bagit implementations.

**External-Identifier:**

 * An identifier for the mailbag as a whole supplied by the agent creating the mailbag. Mapped to PREMIS object, objectIdentifierValue field.

**Mailbag-Agent:**

 * Description of the agent packaging email into a mailbag. Mapped to PREMIS package event, eventDetail@program.

**Mailbag-Agent-Version:**

 * Version of the agent packaging email into a mailbag. Mapped to PREMIS package event, eventDetail@version.


#### 5.2.3 Optional Metadata Fields

**Payload-Oxum:**

 * The number of octets (8-bit bytes) and files in a mailbag. This field is RECOMMENDED for quick mailbag integrity checks. See [Bagit 2.2.2](https://datatracker.ietf.org/doc/html/rfc8493#section-2.2.2) for further details.

**Bag-Size:**

 * A human readable approximate size of the mailbag. See [Bagit 2.2.2](https://datatracker.ietf.org/doc/html/rfc8493#section-2.2.2) for further details.

**Capture-Date:**

 * Timestamp denoting when the email included in a mailbag was originally captured. May be similar to Bagging-Date if a mailbag was packaged using the IMAP protocol. MUST conform to [RFC3339](https://tools.ietf.org/html/rfc3339), such as 1937-01-01T12:00:27.87+00:20. Mapped to PREMIS capture event, eventDateTime field.

**Capture-Agent:**

 * A string field describing the agent used to capture the email included in a mailbag. Mapped to PREMIS capture event, eventDetail@program.

**Capture-Agent-Version:**

 * A string field describing the version of the agent used to capture the email included in a mailbag. Mapped to PREMIS capture event, eventDetail@version.

**IMAP-User:**

 * The username used to connect using the IMAP protocol. IMAP-User SHOULD be used if a mailbag was packaged using IMAP.

**IMAP-Host:**

 * The host used to connect using the IMAP protocol. IMAP-Host SHOULD be used if a mailbag was packaged using IMAP.

**MBOX-Format-Details:**

 * A string field describing the nature of MBOX file(s) included in a mailbag.

**MBOX-Agent:**

 * A string field describing the agent used to create, read, or manipulate MBOX file(s) during the packaging of a mailbag.

**MBOX-Agent-Version:**

 * A string field describing the version of the software agent used to create, read, or manipulate MBOX file(s) during the packaging of a mailbag.

**EML-Format-Details:**

 * A string field describing the nature of EML file(s) included in a mailbag.

**EML-Agent:**

 * A string field describing the agent used to create, read, or manipulate EML file(s) during the packaging of a mailbag.

**EML-Agent-Version:**

 * A string field describing the version of the software agent used to create, read, or manipulate EML file(s) during the packaging of a mailbag.

**PDF-Format-Details:**

 * A string field describing the nature of PDF file(s) included in a mailbag as an email format. This does not refer to any PDF attachment files.

**PDF-Agent:**

 * A string field describing the agent used to create, read, or manipulate PDF file(s) during the packaging of a mailbag.

**PDF-Agent-Version:**

 * A string field describing the version of the software agent used to create, read, or manipulate PDF file(s) during the packaging of a mailbag.

**WARC-Format-Details:**

 * A string field describing the nature of WARC file(s) included in a mailbag.

**WARC-Agent:**

 * A string field describing the agent used to create, read, or manipulate WARC file(s) during the packaging of a mailbag.

**WARC-Agent-Version:**

 * A string field describing the version of the software agent used to create, read, or manipulate WARC file(s) during the packaging of a mailbag.


#### 5.2.4 Example bag-info.txt files

**Example 1 (MBOX source and PDF & WARC derivatives):**


    Bag-Type: Mailbag
    Mailbag-Source: MBOX
	Mailbag-Specification-Version: 1.0
    Original-Included: True
    Bagging-Date: 2021-05-05
	Bagging-Timestamp: 2021-05-05T10:33:54+00:00
    External-Identifier: 6700b2a1-f7fb-48d1-92b3-4ba1f5fc88d0
    Mailbag-Agent: mailbagit
    Mailbag-Agent-Version: 0.3.0
    Capture-Date: 2016-11-16T09:58:31+00:00
    Capture-Agent: Gmail Export
    MBOX-Format-Details: MBOXO
    MBOX-Software-Agent: mailbox
    MBOX-Software-Version: 0.4
    PDF-Format-Details: PDF/A
    PDF-Software-Agent: wkhtmltopdf
    PDF-Software-Version: 0.12.6
    WARC-Format-Details: WARC 1.1
    WARC-Software-Agent: warcio
    WARC-Software-Version: 1.7.4 

**Example 2 (IMAP source and MBOX & EML & PDF & WARC derivatives):**

    Bag-Type: Mailbag
    Mailbag-Source: IMAP
	Mailbag-Specification-Version: 1.0
    Original-Included: False
    Bagging-Date: 2021-05-04
	Bagging-Timestamp: 2021-05-04T18:16:58+00:00
    External-Identifier: 944efc3e-d9df-40ad-8b87-fbb120241ddb
    Mailbag-Agent: mailbagit
    Mailbag-Agent-Version: 0.3.0
    Capture-Date: 2021-05-04T18:16:23+00:00
    Capture-Agent: imaplib
	Capture-Agent-Version: 3.9.12
    MBOX-Format-Details: MBOXO
    MBOX-Software-Agent: mailbox
    MBOX-Software-Version: 0.4
    EML-Format-Details: EML
    EML-Software-Agent: email
    EML-Software-Version: 4.0.2
    PDF-Format-Details: PDF
    PDF-Software-Agent: pyPdf
    PDF-Software-Version: 1.13
    WARC-Format-Details: WARC 1.1
    WARC-Software-Agent: warcio
    WARC-Software-Version: 1.7.4

**Example 3 (PDF source and no derivatives):**


    Bag-Type: Mailbag
    Mailbag-Source: PDF
	Mailbag-Specification-Version: 1.0
    Original-Included: True
    Bagging-Date: 2021-04-14
	Bagging-Timestamp: 2021-04-14T13:33:42+00:00
    External-Identifier: 64bd6983-b0d0-4cd5-9521-d04ee8da3dae
    Mailbag-Agent: mailbagit
    Mailbag-Agent-Version: 0.3.0
    PDF-Format-Details: PDF
    PDF-Software-Agent: wkhtmltopdf
    PDF-Software-Version: 0.12.6

**Example 4 (PST source and MBOX & PDF & WARC derivatives):**


    Bag-Type: Mailbag
    Mailbag-Source: PST
	Mailbag-Specification-Version: 1.0
    Original-Included: True
    Bagging-Date: 2021-03-08
	Bagging-Timestamp: 2020-03-08T22:31:12+00:00
    External-Identifier: 08c91329-12a2-4360-b60a-0075aba3f6fd
    Mailbag-Agent: mailbagit
    Mailbag-Agent-Version: 0.3.0
    PST-Format-Details: 7.2
    PST-Software-Agent: libpff
    PST-Software-Version: 20161119
    MBOX-Format-Details: MBOXO
    MBOX-Software-Agent: mailbox
    MBOX-Software-Version: 0.4
    PDF-Format-Details: PDF
    PDF-Software-Agent: pyPdf
    PDF-Software-Version: 1.13
    WARC-Format-Details: WARC 1.1
    WARC-Software-Agent: warcio
    WARC-Software-Version: 1.7.4


### 5.3 mailbag.csv

A Mailbag MUST contain an additional tag file named “mailbag.csv.” The mailbag.csv file provides the necessary structure for mailbag implementations to connect multiple representations of each message, along with their Mailbag-Message-ID, relative locations within an account’s email folders, and number of attachments. This file MUST list each message included in a mailbag and MAY also contain commonly used email headers.

The mailbag.csv MUST comply with CSV rules described in [Section 5.5](#55-CSV-Tag-Files)

In compliance with [RFC4180](https://tools.ietf.org/html/rfc4180), all records/rows in the mailbag.csv file MUST contain the same number of fields, including the column header. For fields without values, it is RECOMMENDED to use an empty string.


#### 5.3.1 mailbag.csv column headers

A mailbag.csv MUST contain the following column headers in the order that they are listed:

* Error
* Mailbag-Message-ID
* Message-ID
* Original-File
* Message-Path
* Derivatives-Path
* Attachments

Additionally, a mailbag.csv MAY contain the following optional column headers in the order that they are listed:

* Date
* From
* To
* Cc
* Bcc
* Subject
* Content-Type


#### 5.3.2 mailbag.csv columns

**Error (required):**

 * A single string field which is available for implementations to document any warnings or errors found while parsing a message or generating derivatives. The lack of any errors MUST be denoted by an empty string. It is RECOMMENDED for implementations to also create a more comprehensive and well-structured error report outside of a mailbag.

**Mailbag-Message-ID (required):**

 * An identifier created for each email message as it is packaged into a mailbag as described by [Section 3](#3-identifiers).

**Message-ID (required):**

 * The original unique identifier for an email message created and/or managed by the email account prior to its packaging into a mailbag. This identifier is commonly contained within an email’s Message-ID header and is described in [RFC2392](https://tools.ietf.org/html/rfc2392).

**Original-File (required):**

 * The path to the original file for the email message relative to a message’s format subdirectory. This could be the original MBOX, PST, EML, or MSG file for each message and multiple messages should refer to the same file for MBOX and PST sources. This will also include the relative directory path within a mailbag. If the message does not have an original filename, such as if the email source was IMAP, the value of the Original-Filename column MUST be an empty string. 


 * The format of Original-File MUST comply with the rules for Bagit manifests as defined in [Bagit 2.1.3](https://tools.ietf.org/html/rfc8493#section-2.1.3), meaning the slash character ('/') MUST be used as a path separator.

 Examples:

    	Inbox/A free and open internet.eml
    	Listservs/Code4Lib/survey.eml

**Message-Path (required):**

 * The arrangement of the message within its email account. This field may be drawn from an email folder structure within a PST file or read from the X-Folder, X-Gmail-Labels, or other header. If no arrangement is read from these sources, the content of 

 * The format of Message-Path MUST comply with the rules for Bagit manifests as defined in [Bagit 2.1.3](https://tools.ietf.org/html/rfc8493#section-2.1.3), meaning the slash character ('/') MUST be used as a path separator.

 Examples:
 
		Inbox
		Listservs/Code4Lib
		Inbox/*Important*

**Derivatives-Path (required):**

 * The path to all message derivatives relative to a message’s format subdirectory. This is a join of Original-File and Message-Path. The explicit path to all derivatives can be inferred by joining a message’s format subdirectory as defined in [Section 4.1](#41-format-subdirectories) and the Derivatives-Path field.


 * Since a Message-Path may contain characters that are invalid, these characters must be escaped in Derivatives-Path so they are valid as directory names on the filesystem used to package the mailbag. A mailbag is not otherwise opinionated on which escaping method implementations use. A message can be connected to its unescaped arrangement path by mapping Derivatives-Path to Message-Path.


 * A derivative’s filename MUST be the message’s Mailbag-Message-ID as defined in  [Section 4.4.1](#441-message-file-names). The format of Derivatives-Path MUST comply with the rules for Bagit manifests as defined in [Bagit 2.1.3](https://tools.ietf.org/html/rfc8493#section-2.1.3), meaning the slash character ('/') MUST be used as a path separator.

 Examples:

    	Dave/Inbox
    	Fatima/Listservs
    	Inbox/%2AImportant%2A

**Attachments (required):**

 * The number of attachments contained in an email message expressed as an integer. A zero character (0) MUST be used when a message does not contain attachments.

**Date (optional):**

 * The content of an email message’s Date header. The value MUST be an empty string if either the header has no value or is not present.

**From (optional):**

 * The content of an email message’s From header. The value MUST be an empty string if either the header has no value or is not present.

**To (optional):**

 * The content of an email message’s To header. The value MUST be an empty string if either the header has no value or is not present.

**Cc (optional):**

 * The content of an email message’s Cc header. The value MUST be an empty string if either the header has no value or is not present.

**Bcc (optional):**

 * The content of an email message’s Bcc header. The value MUST be an empty string if either the header has no value or is not present.

**Subject (optional):**

 * The content of an email message’s Subject header. The value MUST be an empty string if either the header has no value or is not present.

**Content-Type (optional):**

 * The content of an email message’s Content-Type header. This should be the MIME type for an email and is useful for determining if a message is multipart, such as when email contains both text and HTML representations. The value MUST be an empty string if either the header has no value or is not present.


#### 5.3.3 Splitting large mailbag.csv files

If a mailbag contains more than 100,000 email messages, the mailbag.csv file MUST be split into separate files for every 100,000 messages. These files MUST be named mailbag-x.csv, where x is an incremental integer, starting with 1. The incremental integer MUST also contain leading zeros (0) to ensure it takes up the same number of digits as the highest incremental digit. Thus, the first split file would be mailbag-1.csv for mailbags containing less than 900,001 messages, but would be mailbag-01.csv for mailbags containing 900,001 or more messages when mailbag-10.csv becomes necessary.

Only the first split file (mailbag-1.csv, mailbag-01.csv, etc.) MUST contain column headers, and additional mailbag-x.csv files MUST not contain column headers to preserve the number of rows.


### 5.4 folders_not_retained.txt and messages_not_retained.txt

A mailbag MAY contain two additional tag files used to denote that part of an email account was not retained prior to, or during, packaging. Each line of a messages_not_retained.txt file must contain the Message-ID for a message not present in a mailbag. Each line of a folders_not_retained.txt file must contain an email folder not contained in a mailbag. When email folders not retained were included within another email folder, these folders MUST be expressed as a filepath relative to the account level. The format of the filepath MUST comply with the rules for Bagit manifests as defined in [Bagit 2.1.3](https://tools.ietf.org/html/rfc8493#section-2.1.3). 


#### 5.4.1 Example messages_not_retained.txt

	a6e213b376f34b4f839059e4cc19f2f8@jerrynadler.com
	1709911313.283722191466906290194.JavaMail.app@rbg23.atlis1
	c2617427190b4d0a9d45e8f741d92041@carolynmaloney.com
	20160623144948.9714F608DE@233elwb03.blackmesh.com
	9656357.20160623150335.576bfa478e89a7.63874426@mail1.mcsignup.com
	...


#### 5.4.2 Example folders_not_retained.txt

	Drafts
	Listservs/A&A
	Listservs/ERS
	Trash
	...

### 5.5 CSV Tag Files

CSV tag files in a mailbag are strictly defined to reduce ambiguity as there are multiple ways of writing CSV files. All CSV tag files described in this specification, including mailbag.csv and attachment.csv files, MUST be UTF-8 endoded, MUST use a comma (,) as a delimiter, MUST use straight double quotes (") as quote characters for all fields, and MUST NOT use an additional escape character. In compliance with [RFC4180](https://tools.ietf.org/html/rfc4180), the mailbag.csv and attachment.csv files MUST also use carriage return and line feed characters (CRLF or \r\n) for the record delimiter denoting line endings. This means that these files will use a different line endings than the other plain text tag files in a mailbag, which MUST use a line feed character (LF or \n) for line endings as described in [Section 5](#5-tag-files). This requirement tries to meet the default csv dialect as expected by the Python csv library and that implementation should be used as guidance on any further ambiguities.

It is RECOMMENDED that other CSV tag files within a mailbag follow these same rules.  

## 6. References

**[RFC2119]**
Bradner, S., "Key words for use in RFCs to Indicate Requirement Levels", IETF, March 1997.
DOI 10.17487/RFC2119
URL: [https://tools.ietf.org/html/rfc2119](https://tools.ietf.org/html/rfc2119)

**[RFC2392]**
Levinson, E., “Content-ID and Message-ID Uniform Resource Locators,” IETF, August 1998.
DOI 10.17487/RFC2392
URL:[ https://tools.ietf.org/html/rfc2392](https://tools.ietf.org/html/rfc2392)

**[RFC3339]**
Klyne, G, Newman, C., “Date and Time on the Internet: Timestamps,” IETF, July 2002.
DOI 10.17487/RFC3339
URL: [https://tools.ietf.org/html/rfc3339](https://tools.ietf.org/html/rfc3339)

**[RFC4180]**
Shafranovich, Y., “Common Format and MIME Type for Comma-Separated Values (CSV) Files,” IETF, October 2005.
DOI 10.17487/RFC4180
URL: [https://tools.ietf.org/html/rfc4180](https://tools.ietf.org/html/rfc4180)

**[RFC8174]**
Leiba, B., "Ambiguity of Uppercase vs Lowercase in RFC 2119 Key Words", IETF, May 2017.
DOI 10.17487/RFC8174
URL: [https://tools.ietf.org/html/rfc8174](https://tools.ietf.org/html/rfc8174)

**[RFC8493]**
Kunze, J., Littman, J., Madden, E., Scancella, J., Adams, C., “The BagIt File Packaging Format (V1.0),” IETF. October 2018.
DOI 10.17487/RFC8493
URL:[ https://tools.ietf.org/html/rfc8493](https://tools.ietf.org/html/rfc8493)
