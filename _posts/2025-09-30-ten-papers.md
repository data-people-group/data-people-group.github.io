---
layout: post
title: The First Decade as Faculty - Looking Back
date: 2025-09-30
authors: <a href="https://people.eecs.berkeley.edu/~adityagp/">Aditya G. Parameswaran</a>
summary: Aditya reflects on his greatest hits from the first decade of facultyhood.
categories: blogs
---

My first decade as faculty ended in  August 2024. 
Assuming a typical career of four decades, this would mean that I've completed 
the first quarter of my faculty career. 
So I wanted to share my ten favorite papers from this rather substantial period, 
with a bit of reflection and backstory. 
_And yes, I know August 2024 happened a year ago—with this blog post languishing as a draft since, but better late than never!_

Onto the ten papers; I'll discuss them in three  buckets: 
visualization (3 papers), spreadsheets (3 papers), and miscellaneous—one paper each on data versioning, 
dataframes, scalable ML, and notebooks.
I will then discuss themes and takeaways from the decade. 

## Visualization


### 1. SeeDB, VLDB 2015

I got interested in visualization after watching [Jeff Heer](https://homes.cs.washington.edu/~jheer/) 
give a talk at the end of my PhD—specifically around trying to make visual data analysis more automated and scalable. 
The [SeeDB paper](https://dl.acm.org/doi/10.14778/2831360.2831371), led by [Manasi Vartak](https://www.linkedin.com/in/manasi-vartak/), 
was one of the first research efforts I kick-started as a postdoc with [Sam Madden](https://db.csail.mit.edu/madden/) and continued as faculty, and one of the very first papers on _visualization recommendations_—accelerating analysis by automatically suggesting interesting visualizations to users. SeeDB prioritized data-centric differences, basically, recommending visualizations if it showed a pattern that deviated from the rest. 
The key nugget was casting visualization recommendation as _search through a large space of SQL queries (one per visualization)_: SeeDB pioneered the use of multi-query optimization and approximate query processing to search through this space efficiently. 

Due to SeeDB and subsequent efforts, visualization recommendation is everywhere; popular BI tools like PowerBI (via [QuickInsights](https://dl.acm.org/doi/abs/10.1145/3299869.3314037)) 
and Tableau (via [AskData](https://help.tableau.com/current/pro/desktop/en-us/ask_data.htm)/[ExplainData](https://www.tableau.com/blog/available-today-explain-data-tableau-catalog-and-tableau-server-management-add)) 
support variants of it. In particular, PowerBI uses an approach to recommend visualizations that is very similar to SeeDB.

I suspect we're going to only see SeeDB's ideas of efficiently churning through a space of visualizations to find insights rise in importance in the era of [agents performing data analysis on our behalf](https://arxiv.org/abs/2509.00997)!  In particular, multi-query optimization and approximate query processing is essential to ensure that agents (which can generate 1000s of queries a second) can make progress quickly. 


### 2. Zenvisage, VLDB 2017

[Zenvisage](https://www.vldb.org/pvldb/vol10/p457-siddiqui.pdf), led by [Tarique Siddiqui](https://www.linkedin.com/in/tariquesdd/), was our second crack at visualization recommendation.  With Zenvisage, which was developed in collaboration with experts in genomics, astrophysics, battery science, we built on SeeDB ideas to ask: _instead of just providing visualization recommendations, what if we could support active search for visualizations, based on patterns_?  

With the Zenvisage UI, users could drag and drop a visualization onto a canvas to "search" for similar visualizations, or could sketch a pattern. Zenvisage also introduced a query language, ZQL (yes, "zee quel"), to comb through visualization collections based on various criteria.  SeeDB queries were simply a special case of ZQL. The "ultimate query" in ZQL was akin to: _for all products whose sales trends are similar to those of chair, find those whose profits have increased the most_ — still cool when I think about it years later. Admittedly ZQL was ahead of its time; maybe we'll have a rich data analysis language that admits reasoning about high-level patterns at some point!

An accompanying paper on the design process that led to Zenvisage was published at VIS with my favorite paper title of all time: [*You can't always sketch what you want*](https://arxiv.org/abs/1710.00763) (i.e., sketch-based querying is insufficient). Tarique's next paper on extending Zenvisage to support approximate search for similar shapes, [ShapeSearch](https://dl.acm.org/doi/10.1145/3318464.3389722), won the SIGMOD best paper award. 


### 3. Lux, VLDB 2022

Our third major visualization recommendation effort, [Lux](https://arxiv.org/abs/2105.00121), led by [Doris Lee](https://www.linkedin.com/in/dorisjlee/), gained significant adoption. 
The main missing ingredient from our past efforts was that they weren't integrated into existing user workflows, leading
to them being not as _sticky_ as a result. As an academic group, we couldn't really get our features embedded within BI tools (even if the ideas themselves were adopted, see above), so we did the next best thing—we built an open-source visualization recommendation tool that wraps around pandas and generates visualizations "on the fly" whenever you print your dataframe. 

Easier said than done, as dataframes are messy, with data in various stages of cleaning, and have heterogeneous data types. Lux leveraged clever caching and background computation to make the process interactive, with the ability to pin a set of visualizations as "views" to be tracked through data wrangling. Users were always in control: they could export the recommendations as code, and could further tailor them based on their preferences, expressed via code or interactions. 

Lux was used by data scientists across a whole host of disciplines and industries, with over 750K downloads. My favorite example of a Lux user was a doctor who was self-taught and ended up using Lux to explore hospital visit data. Interestingly, one of the most commonly requested features was the ability to export visualizations as images, so that the users could embed them into their powerpoint presentations.

## Spreadsheets

### 4. Dataspread, ICDE 2018

Early on at Illinois, [Mangesh Bendre](https://www.linkedin.com/in/mangesh-bendre/) got me excited about spreadsheets; 
how they were understudied, and how, if 
only we could figure out a way to make them better, 
we could impact the lives of so many of those who rely on them as their primary data management and analysis system.
Enter [Dataspread](https://ieeexplore.ieee.org/stamp/stamp.jsp?arnumber=8509241): the goal was to build a scalable spreadsheet—unconstrained by limitations of 
current spreadsheets (e.g., capped at a million rows), and is interactive at scale. 

Our first bite of this apple came with our effort to redesign the storage layer for spreadsheets. 
We developed a hybrid data model for spreadsheets, where one could represent tabular regions differently from ad-hoc formulae and value scattered in the spreadsheet, with the latter being represented in a key-value format, where the key was the pair (row #, column #). But my favorite contribution from that paper was an index that supports ordered lookup (remember, spreadsheets are ordered!) with updates (rows/columns being added/deleted), 
using a variant of B+trees, the [counted B+tree](https://www.chiark.greenend.org.uk/~sgtatham/algorithms/cbtree.html).
Sadly, this paper took a while to get published—often, the first paper in a new area has that issue, but I'm still proud of the result. 

### 5. Anti-freeze, SIGMOD 2019

Moving beyond storage, the [second Dataspread paper](https://dl.acm.org/doi/10.1145/3299869.3319876), led by [Tana Wattanawaroon](https://en.eng.ku.ac.th/?professor=dr-tana-wattanawaroon) and Mangesh, was on formula computation. 
On complex spreadsheets, formula computation is a pain: spreadsheet systems will simply freeze until the computation is complete. 

We realized that there was a better way: after a change, instead of hanging until computation was complete, 
we could _hide away in-progress cells with progress bars, and return all other unaffected cells immediately_. 
Determining which cells were affected wasn't easy, nor was determining how to compute the affected ones. 
One of my favorite contributions in this work 
was a new metric for interactivity that we called "_availability_": the area under the curve of the number of cells a user could act on over time. 

This paper later influenced work on [transactional panorama](https://www.vldb.org/pvldb/vol16/p1494-tang.pdf), led by Dixin Tang, 
where we explored looser notions of consistency for BI tools, 
balancing what the user can act on, how up-to-date the results are (and whether they "move forward in time"), 
and how consistent they are with each other. This work (which won a "best of VLDB") opens up a new research 
direction of transactions meets UX....if only someone would pick it back up!

### 6. Benchmarking, SIGMOD 2020

As we were working on Dataspread, we realized: we didn't know much about performance of present-day spreadsheets on various workloads. 
[Sajjadur Rahman](https://sajjadur.net/) led [the effort to benchmark](https://dl.acm.org/doi/10.1145/3318464.3389782) Excel, Sheets, and OpenOffice Calc on various workloads. 
Spreadsheets were awful, becoming _unresponsive on as few as 50,000 rows_.   To do so, Sajjadur had to contend with the sparse documentation for all of these different spreadsheets; figuring out how to instrument them to collect timing information: a real pain.  I quote stats from this paper often, and to date, this paper is our best understanding of spreadsheet performance—and implementation. 


## Miscellaneous

### 7. OrpheusDB, VLDB 2017

The genesis of Orpheus was the [Datahub project](https://people.csail.mit.edu/anantb/public/docs/research/datahub/datahub-cidr-15.pdf) at MIT, from when [Aaron Elmore](https://people.cs.uchicago.edu/~aelmore/) and I were postdocs with [Sam Madden](https://db.csail.mit.edu/madden/).  One big concern that emerged in our work then was that it's hard to convince people to use an entirely new system _just_ for data versioning — _so how much could we repurpose existing database systems to support data versioning in a "bolt-on" fashion_? (As in Lux, not requiring users to change existing workflows is a recurring theme.) 

[Silu Huang](https://www.linkedin.com/in/silu-huang-75b98a64/) and [Liqi Xu](https://www.linkedin.com/in/liqi-xu-00133849/) led the charge in developing [OrpheusDB](https://arxiv.org/abs/1703.02475): they showed that diff-based representations, as in source-code versioning systems, are ineffective in supporting advanced queries or retrieval on versions. Instead, representations that more directly encode the bipartite graph between versions and tuples is the way to go, coupled with intelligent partitioning to reduce redundancy. What I liked about the paper was that it covered a lot of ground: new query constructs, new data models, new storage optimization frameworks—and had some nice theory to boot. This paper was originally rejected at SIGMOD, but then got a "best of conference" at VLDB; go figure. 

### 8. Modin, VLDB 2022

This [paper](https://www.vldb.org/pvldb/vol15/p739-petersohn.pdf), led by [Devin Petersohn](https://www.linkedin.com/in/devinpetersohn/) and [Dixin Tang](https://dx-tang.github.io/), was our second paper on dataframes, as part of our effort to develop [Modin](https://github.com/modin-project/modin), a scalable drop-in replacement for pandas. As in Orpheus and Lux, the drop-in bit was important: this meant users could simply reuse their existing pandas scripts. Our first paper on [scalable dataframes](https://arxiv.org/abs/2001.00888) in VLDB 2020 laid out an algebra for dataframes showing how they support operations across rows, columns (e.g., filter columns with null values), or even blocks (e.g., regex replace across the dataframe).  But figuring out how to optimize computation with this new set of primitives was challenging.

In this paper, we figured out how to parallelize individual operators, and then how to use that to optimize entire pipelines. We introduced row, column, and block-wise decomposition—i.e., operating on groups of rows, columns or blocks in parallel, depending on the operator—and optimizing dataframe pipelines with sequences of operators by selecting the right decomposition at each stage—with appropriate pipelining or shuffle operators in-between. 

This paper laid the groundwork for Modin's remarkable open-source success: at the time of publication, at the 1M downloads mark, but now at _46M(!)_.  Modin, along with Lux (above), led Devin, Doris, and I to start Ponder, in 2021, centered around better data science tooling—and was then [acquired by Snowflake](https://www.snowflake.com/en/blog/snowflake-to-acquire-ponder/) in October 2023. Modin now forms the basis of Snowflake's dataframe offering.




### 9. Helix,  VLDB 2019

This [paper](https://www.vldb.org/pvldb/vol12/p446-xin.pdf), led by [Doris Xin](https://www.linkedin.com/in/doris-xin/), was my first foray into machine learning proper. Doris was excited about exploring human-in-the-loop machine learning. We identified an opportunity: users often make small tweaks to their pipeline, but end up rerunning the entire pipeline from scratch. _What if we could avoid running what wasn't necessary, reusing results from previous iterations?_ Helix's core problem ended up getting cast as an elegant max-flow/min-cut problem that makes a decision for each step: do you re-read results from disk, or do you recompute given inputs?  After Helix, Doris worked on a [survey on how people use AutoML](https://arxiv.org/abs/2101.04834); our first such industry survey-based paper that ended up at CHI, and also serving as a template for other work in the group that happened later. 

### 10. NBSafety, VLDB 2021

My student [Stephen Macke](https://smacke.net/) got inspired by watching Joel Grus' talk on how notebooks [sucked](https://docs.google.com/presentation/u/1/d/1n2RlMdmv1p25Xy5thJUhkKGvjtV-dkAIsUXP-AL4ffI/edit?pli=1&authuser=1) and wanted to fix them. In particular, we started with reproducibility and the fact that in notebooks, if you (as a user) don't remember the dependencies between cells, there is a very real danger of you forgetting to run the cells in the right sequence, resulting in downstream errors and lack of reproducibility. With [nbsafety](https://arxiv.org/abs/2012.06981), users get lightweight cues alongside the notebook based on whether a cell is safe to be rerun (i.e., all its dependencies have been rerun), or unsafe, as green or red highlights. This small UI change had to be enabled by a lot of work on the PL side, specifically using a combination of static and dynamic analysis.

Nbsafety was successful as an open-source project, with over 350K+ downloads. Follow-on work also explored automatically extracting a [reproducible snippet for a specific artifact](https://smacke.net/papers/nbslicer.pdf).  Nbsafety and other projects also [led](https://marimo.io/) [to](https://hex.tech/blog/hex-two-point-oh/) [notebooks](https://observablehq.com/platform/notebooks) offering an alternate paradigm of reactivity where all downstream cells are automatically executed, as opposed to manual execution.  


## Reflections and takeaways
Overall, the papers above covered most years in the 2014—2024 decade, except a few (2016, 2023), with no year having more than two papers. And they covered the vast majority of students who worked with me in that period, including Devin, Dixin, Doris L, Doris X, Liqi, Manasi, Mangesh, Sajjadur, Silu, Stephen, Tana, and Tarique—an amazing group, who are all leading the way in industry or academia. 

Now, for some reflections from that period:

- _Drop-in/bolt-on is the way to go_: Better technology doesn't mean more adoption. Given humans don't typically want to change their tools or workflows, it's better to try to make those existing tools or workflows better, rather than trying to force users to change their approach. While I don't think this is universally applicable (otherwise we'll just keep using the same tools over time), it is one important strategy to make sure one's work is useful. 
- *Working with end-users is incredibly satisfying*: throughout this decade, we sought out users—be it groups in other disciplines or in industry, or the open-source community at large. Engaging users early helped us make sure that we weren't solving non-problems. But to work effectively with users, we had to get trained in HCI: I started this decade without knowing how to do a user study, and figured it out as I went along, with ample help from [Karrie Karahalios](https://en.wikipedia.org/wiki/Karrie_Karahalios). 
- _Understudied spaces often have the richest problems_, as we learned from looking at spreadsheets, dataframes, or notebooks, none of which had an established sub-community of researchers. While figuring out how to publish in these spaces is an entirely different manner, the lack of competition makes it such that you can take your time to publish—and when you do, it is more likely to define the field.
- _It's hard to predict when ideas will be useful_: as an example, ideas from SeeDB are seeing a resurgence in the era of agentic AI, where agents similarly want to issue a massive number of speculative probes to a backend data system, as is Orpheus, since agents will need lightweight branches and merges when operating on data at scale.
- _The push-pull of academia_: In writing up this post, I realized that I got pulled in so many directions by students — as often as I pulled them. Working with students in places like Illinois or Berkeley is such a blessing, you learn so very much. 
- _Building big, ambitious systems is fun and challenging!_ In picking papers to list, I realize I mostly listed big system building efforts, rather than other more algorithmic papers. While my PhD centered on more algorithmically-inclined work, I found, in my postdoc and beyond that system building efforts were a lot more exciting and had greater impact—in no small part because we thought long and hard about how the systems ended up getting adopted and used!

What a fun decade — now onto the next one!



