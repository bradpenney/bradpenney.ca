---
title: "Python Pandas Improves Spreadsheet Workflows"
date: 2022-12-08
slug: "pythonPandas"
description: "Python Pandas Improves Spreadsheet Workflows"
keywords: ["python", "pandas", "merge"]
draft: false
#tags: ["python", "pandas", "merge"]
math: false
toc: true
---

Imagine a scenario where every day all day you and your team work on tickets resolving issues/bugs that have arisen in your infrastructure or your source code. These issues are highlighted by an automated nightly scan that spits out a dreaded spreadsheet every morning, highlighting all the detected issues. In an attempt to control the chaos, your team has cleverly decided to track these in a shared spreadsheet called the Master Tracker.

## Problem: Master Tracker Has Custom Information

Your team has added columns to the auto-generated spreadsheet to track issues more efficiently.  Adding information such as who is working on each ticket, whether there are other teams/resources involved, and of course a ticket number from the change management system.  The Master Tracker now looks like this:

![Master Ticket Tracker](/devops/pandas_intro/master_workbook.png)

Unfortunately, the spreadsheet that you receive every morning **includes** all the issues the team is already working on, but it is missing the columns that the team has added for tracking purposes:

![Added Issues](/devops/pandas_intro/added_tickets.png)

In the tiny (optimistic!) example above, there are only 2 new issues, which is no biggie for copy/paste aficionados.  Sadly, this optimism might be far-fetched, as some mornings the team wakes up to 50 new issues!  This, combined with the fact that the Master Tracker could have anywhere between 2 and 200 issues at the end of the previous day means that comparing these worksheets, determining which are new issues and merging these via copy/paste would be **extremely** tedious.  The goal is to merge the new issues reported into the existing Master Tracker without losing any data the team added.

## Solution: Python Pandas Merge()

Python Pandas has a very workable solution for this problem.  In a nutshell, both worksheets are imported as separate Pandas DataFrames, then we perform a `merge` that combines the two dataframes without losing any data.  Those rows that have the same info will not be overwritten, and new rows will receive a `NaN` in the missing columns (which translates into a blank cell once converted back to a spreadsheet).

I'll use a Jupyter Notebook to demonstrate this procedure, but of course this could just as easily be done with a pure text script.  The advantage of Jupyter Notebook is that it has built-in breakpoints, so we can confirm each step executed successfully before running the next.

### Getting Started

To start with we need to `import pandas` as well as import each spreadsheet as a Pandas DataFrame:

![Initial Setup](/devops/pandas_intro/initial_setup.png)

Next, we'll display the starting files to confirm that the dataframes loaded as expected.  Note the use of the `.head()` method to only show the first 10 rows (in case either DataFrame is excessively long).

![Display Starting DataFrames](/devops/pandas_intro/display_starting_files.png)

### Determine Expected Results

To ensure we don't have some very strange results, lets do a quick check to see how many additional rows will be added/removed from the tracker.  

![Pre-Merge](/devops/pandas_intro/pre-merge_check.png)

Side Note: The expectation here is that this will be a positive number.  As issues are resolved, they're removed from the Master Tracker.  If the number is negative, then some issues were unexpected cleared up (wahoo!) but that will still require manually finding and removing them, at least while using `merge` with `outer join`.

### The Magic of Pandas `merge`

Its time to bring the magic of Pandas `merge`.  We're going to merge the two DataFrames together with an *outer join*, which will preserve all information from both DataFrames.  The command is simple - but note that best practice is to create a third Data Frame for the results.  This means we still have our two original Data Frames in case there is some problem.  Obviously this depends on data size and system resources.

A quick note about joins - Pandas supports the standard joins that will be familiar to those who use SQL in relational databases.  The positioning of the arguments in the function call actually determines left and right position, so pay attention to which Data Frame is on the right and which is on the left (if using those types of joins).  In our case, we want to make sure we keep everything, so an outer join makes the most sense.  Here's a standard representation of how joins work in Pandas (and SQL):

![Pandas Merge Options](/devops/pandas_intro/joins.png)

The Python Pandas statement to join the two DataFrames is more simple than SQL, with the `how` keyword argument doing all the heavy lifting:

![Merge Data Frames](/devops/pandas_intro/merge_data_frames.png)

### Validate the Results

Once the `merge` command returns without error, it is time to check the results.  A quick `head()` method shows the new DataFrame, and then a simple `if/else` logic check ensures the arithmetic works out as expected:

![Validate Results](/devops/pandas_intro/validate_results.png)

### Write the DataFrame to the Spreadsheet

Once we've gone through the results and are satisfied that no catastrophe has befell our Master Tracker data, we're ready to write back to the file:

![Write to the Master Tracker](/devops/pandas_intro/write_new_file.png)

After a tiny bit of formatting inside the spreadsheet program, it is clear that the additional tickets are now included in the Master Tracker, waiting for team-generated values to be added:

![New Tickets Now Included](/devops/pandas_intro/new_tickets_now_included.png)

## In Conclusion

This brief article barely scratches the surface of how Python Pandas is useful for automating tedious day-to-day nuisance chores.  It is also one of the most critical tools for a Data Scientist to use and be comfortable with.  Feel free to consult [Pandas](https://pandas.pydata.org/) for tons more information; I highly encourage you to dive deep to add Python Pandas to your tool belt!
