# UChicago DSI Cluster Information Site

This repository contains the Jekyll-based website for information regarding the DSI's GPU-based cluster. The site provides information about cluster resources, policies, and tools.

You can find the web page here: [https://cluster-policy.ds.uchicago.edu/](https://cluster-policy.ds.uchicago.edu/)

**Note that the content of this repo is used to populate that site and is not useful on its own**

## Development

### Prerequisites

You can run this website using Docker (and `make`). Make sure you have Docker installed on your system.

### Running Locally

To run the site locally, use the following commands:

```bash
# Build the Docker image
make build

# Run the site locally
make serve
```

The site will be available at http://localhost:4000.

### Interactive Shell

To get an interactive shell inside the Docker container:

```bash
make inter
```

### Debugging

For debugging with trace information:

```bash
make trace
```

### Cleaning

To clean up Docker resources:

```bash
make clean
```

### Full Rebuild

To perform a full rebuild and restart:

```bash
make rebuild
```

## Updating Content

Content is stored in Markdown files within the `documents/` directory. After updating content, rebuild and run the site to see your changes.

### Publications List Update Guide

**Last Updated:** September 3, 2026

This guide outlines the steps to add new papers to the research page publications list.

### Steps to Update

1. **Gather Required Information**
   Collect the following details for each new paper you want to add:
   * **Contact (PI):** Principal Investigator or primary contact.
   * **Paper:** The title of the paper.
   * **Link:** Direct URL to the publication (e.g., arXiv, OpenReview, journal site).
   * **Google_Scholar_Link:** Google Scholar profile link for the PI.
   * **Year:** Publication year.
   * **Author_Names:** Full list of authors.
   * **Citation:** Fully formatted citation string (e.g., APA style).

2. **Navigate to the File**
   Locate the publications data file in the repository at the following path:
   `_data/publications.csv`

3. **Append the New Entries**
   Add your new entries to the bottom of the CSV file. Ensure you maintain the existing formatting and column order:
   * **`Sr_No`:** This is the first column and must be a sequential numerical count. Check the last entry in the CSV and increment from there.
   * **Trailing Boolean Column:** At this time, the specific functionality of the final column (which contains `TRUE` or `FALSE`) is not fully defined for the frontend. 

**CSV Format Example:**
```csv
Sr_No,Contact,Paper,Link,Google_Scholar_Link,Year,Author_Names,Citation,[Boolean]
144,PI Name,Paper Title,https://link.com,https://scholar.google.com/...,2026,"Author 1, Author 2",Citation,TRUE
```

## Deployment

This site is configured to be deployed using GitHub Pages. When changes are pushed to the main branch, GitHub will automatically build and deploy the site.
