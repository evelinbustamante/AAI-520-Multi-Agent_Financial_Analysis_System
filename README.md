# AAI-520-Multi-Agent_Financial_Analysis_System
Group 3

Evelin Bustamante, Paola Marsal, Andrew Fennimore


What this is
For our final project we built an AI agent that researches a stock the way
a junior analyst would. You give it a ticker (we mostly used AAPL) and it
decides what to look up, pulls live data from Yahoo Finance, reads a news
article, writes up what it means, checks its own write-up, and saves notes
so the next run on the same stock has something to start from.

Everything is in one notebook, Final_Project.ipynb. The agent_memory folder
holds the notes the agent saves for each ticker.


How it works
The agent starts by asking Llama 3.2 which tools to use and in what order,
then runs them one at a time. The tools are get_stock_data (price, sector,
market cap, 52-week range, recent prices), get_key_financials (revenue, net
income, operating income, EPS), and load_memory (notes from earlier runs).
This is the Agent Core section, and it covers the planning and tool use
requirements.

News goes through a five step prompt chain: ingest the article, clean it
up, classify it as earnings, company news, or market news, pull out the key
facts, and summarize them. This is the Prompt Chaining section.

The category from the chain decides which specialist looks at it next: a
fundamentals analyst for earnings, a news analyst for company news, or a
market analyst for market news. Anything unexpected goes to the news
analyst. This is the Routing section.

Before an analysis is final, an evaluator grades it and an optimizer
rewrites it using the feedback, for up to three rounds. The grade comes
from Llama plus a simple Python check that every dollar amount in the draft
is actually in the data and that price targets aren't passed off as the
current price. We added the Python check because the model was too easy on
its own writing. This is the Evaluator-Optimizer section, and it covers
self-reflection.

For learning across runs, the agent saves a dated note for each ticker to a
JSON file and reads it back the next time. It compares today's price to the
last run, and the specialists get the recent notes as context. This is the
Memory Across Runs section.


Running it

Llama 3.2 is gated on Hugging Face, so first request access to
meta-llama/Llama-3.2-3B-Instruct and create an access token. The first run
downloads about 6.4 GB of model weights.

On your own computer: install Python 3.10 or newer and run

    pip install yfinance transformers accelerate torch huggingface_hub matplotlib jupyter

then log in to Hugging Face with "hf auth login" (or set HF_TOKEN as an
environment variable) and run all the cells in Jupyter or VS Code.


Data and model
All the stock data comes from Yahoo Finance through the yfinance package,
which is free and doesn't need an API key. The model is Llama 3.2 3B
Instruct, run through Hugging Face transformers with sampling turned off so
the results are repeatable.


Who did what
Evelin: the prompt chaining pipeline.

Paola: the Yahoo Finance data tools, memory, routing and the specialist
analysts, error handling, and the system diagram and price chart. Paola is
also our team rep.

Andrew: the agent core (planner, tool registry, and agent loop), the
evaluator-optimizer, and this README.


Known issues
- We mostly tested with AAPL.
- Llama 3.2 3B is a small model. It sometimes treats analyst price targets
  as the current price, which is why the evaluator has the number check.
- In Colab the agent_memory folder is wiped when the runtime resets unless
  you save it back to GitHub or Drive.
- yfinance news only gives a headline and a short summary, so the prompt
  chaining demo uses a full article we copied from Yahoo Finance on
  October 1, 2026.
- A full run on a CPU takes a while.
