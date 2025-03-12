# Scaling Up and Out: Data Transformation With DuckDB and Ray

This is a set of exercises and notebooks for the OxAI 2nd Mini-Conference,
16 March 2025.

## Introduction

In my experience with startups I have noticed a few trends with young
developers coming from university or their first development jobs, and I've
noticed a few trends with startups who begin with tools that don't grow with them because they don't support sufficiently abstract interfaces or force vendor lock-in.

Data manipulation is generally taught and learned with small samples, often in simplistic formats, and on sizes which fit in memory, which leads to a lot of developers who can slurp up CSV files into pandas, but run out of options when they run out of memory and have difficulty scaling their work when the data gets larger.  When my current startup begaing seeing data with higher cardinality, from millions to now over twenty billion rows, learning how to scale our work became important.

Two of the finest tools available in this space as of 20250316 are DuckDB (http://duckdb.org) and ray (http://ray.io).

## Scaling UP: DuckDB--If it looks like a database and quacks like a database...

DuckDB is an in-memory database which at first looks like an OLAP (Online Analytical Processing) version of something like SQLite: it's small, tight, works on a single node, and is easily embedded in any python project.

That belies its true power: Abstraction.  DuckDB allows you to abstract almost any data source as an SQL-queriable database, and it scales "up" extremely well; by that I mean it scales with memory and CPU threads on a single node, and single nodes on a cloud tend to follow linear price scaling.  This allows implementation to vary behind interface and your data store to grow and change with minimal modifications to code, and allows you to operate on data greater than your computer's memory.

## Scaling OUT: Ray--the world is your cluster

In some cases, scaling up reaches its limits, and here DuckDB fails you.  Ray is a general elegant framework for remote execution which has extended itself to data manipulation and machine learning.

Ray represents another abstraction: all code can be made multiprocess and multinode and run that way on either a local machine or a heterogenous cluster.

We operate much of Ray behind Anyscale, representing yet another abstraction: multicloud or multicluster services behind a single management framework, which can reduce the initial friction of DevOps for a machine learning startup.

The catch is that Ray operates on a very "table-oriented" or "row-oriented" basis, so the code tends to look very pythonic and you lose the abstraction of addressing data transforms through SQL.  On the other hand, the scaling works both up and out, and there's no reason your whole data pipeline needs to homogenous.

## Scaling Both Ways: smallpond--You probably don't need it, but good to know it's there if you do

On 28 Feb DeepSeek released both their Fire-Flyer File System (https://github.com/deepseek-ai/3FS) and the Smallpond Data Processing Framework (https://github.com/deepseek-ai/smallpond).  These currently use Ray as an underlying execution engine.

If your data needs are under 10TB, smallpond and 3fs probably won't help you much and in fact will probably be slower than the above alternatives.  But it's good to know it's there!

## Setting Up the Environment

Python package management is a dumpster fire; always has been and likely always will be.  At the present date, the uv package manager (https://github.com/astral-sh/uv) has my favored combination of features, so we'll be using that.  Follow the installation instructions to install uv; quoted from that page, I'd recommend the native installation

```
# On macOS and Linux.
curl -LsSf https://astral.sh/uv/install.sh | sh
```

or
```
# On Windows.
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

Although if you have a system python installed, `pip install uv` can work (or via pipx, ie `pipx install uv`).  The uv manager handles python version installation, small virtual environment management, project management, and tool use so makes a viable replacement for pip and pipx.

Once that's installed, be at the root of this repository and

```
❯ uv venv
Using CPython 3.12.7
Creating virtual environment at: .venv
Activate with: source .venv/bin/activate (or shell-specific)

projects/scaling_up_and_out on  main
❯ source .venv/bin/activate (or shell-specific)

projects/scaling_up_and_out on  main (scaling_up_and_out)
❯ uv sync
Resolved 136 packages in 0.42ms
Installed 131 packages in 63ms
...

❯
```
