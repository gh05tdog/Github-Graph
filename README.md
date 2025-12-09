# GitHub Developer Network Analysis

A Python-based tool for exploring and analyzing collaboration patterns in GitHub developer networks through graph theory, community detection, and semantic analysis.

## Overview

This project constructs and analyzes developer collaboration networks from GitHub repositories. By treating developers as nodes and shared repository contributions as edges, it reveals hidden structures, community patterns, and technical ecosystems within the GitHub platform.

## Key Features

### Network Construction
- **Automated Graph Building**: BFS-based exploration starting from a seed user
- **Collaborative Edge Detection**: Connects developers who contribute to the same repositories
- **Bot Filtering**: Automatically excludes bot accounts from the network
- **Resumable Collection**: Saves progress periodically to handle API rate limits

### Network Analysis
- **Global Network Properties**: Density, clustering coefficient, degree distribution, and assortativity
- **Community Detection**: Louvain algorithm implementation to identify structural communities
- **Centrality Metrics**: Degree and betweenness centrality analysis within communities
- **Language Ecosystem Mapping**: Tracks programming language distributions across the network

### Semantic Analysis
- **README Text Mining**: Extracts and processes project documentation
- **TF-IDF Analysis**: Identifies distinctive vocabulary for each community
- **Sentiment Analysis**: VADER-based sentiment scoring of documentation
- **Word Cloud Visualization**: Visual representation of community-specific terminology

## Tech Stack

- **Core Libraries**:
  - `networkx` - Graph construction and analysis
  - `aiohttp` - Asynchronous GitHub API requests
  - `scikit-learn` - TF-IDF vectorization
  - `nltk` - Natural language processing and sentiment analysis
  
- **Visualization**:
  - `matplotlib` - Plotting and charts
  - `wordcloud` - Word cloud generation
  
- **Data Management**:
  - GEXF format for graph storage
  - JSON for repository metadata

## Installation

1. Clone the repository:
```bash
git clone <repository-url>
cd Github-Graph
```

2. Create a virtual environment:
```bash
python -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate
```

3. Install dependencies:
```bash
pip install networkx aiohttp scikit-learn nltk matplotlib wordcloud python-dotenv
```

4. Set up GitHub API token:
Create a `.env` file in the project root:
```
TOKEN=your_github_token_here
```

## Usage

### Data Collection

Configure collection parameters in the notebook:
```python
SEED_USER = "gh05tdog"          # Starting point for BFS exploration
DEPTH = 2                        # Exploration depth
REPO_LIMIT = 40                  # Max repositories per user
CONTRIBUTOR_LIMIT = 400          # Max contributors per repository
```

Run the graph building process:
```python
G, user_languages = await build_graph(
    SEED_USER, 
    depth=DEPTH, 
    contrib_limit=CONTRIBUTOR_LIMIT,
    repo_limit=REPO_LIMIT
)
```

The exploration creates two main outputs:
- `github_graph.gexf` - The developer collaboration network
- `repo_data.json` - Repository metadata including language statistics and README content

### Network Analysis

**Load and analyze the network:**
```python
G = nx.read_gexf("github_graph.gexf")
largest_cc = max(nx.connected_components(G), key=len)
G_main = G.subgraph(largest_cc).copy()
```

**Compute global statistics:**
```python
density = nx.density(G_main)
clustering = nx.average_clustering(G_main)
assortativity = nx.degree_assortativity_coefficient(G_main)
```

**Detect communities:**
```python
communities = nx.community.louvain_communities(G_main, seed=42)
modularity = nx.community.modularity(G_main, communities)
```

**Analyze language ecosystems:**
```python
# Map repositories to communities
# Aggregate language byte counts
# Calculate language purity scores
```

### Semantic Analysis

**Extract and process README texts:**
```python
# Clean text: lowercase, remove markup, filter stopwords
# Compute TF-IDF scores per community
# Generate word clouds
# Perform sentiment analysis using VADER
```

## Example Results

From a typical analysis of ~48,000 developers and ~2.3 million connections:

- **Network Sparsity**: 0.2% density with high clustering (0.92 coefficient)
- **Community Structure**: 77 distinct communities with 0.86 modularity
- **Language Alignment**: Average 64.5% language purity across communities
- **Sentiment**: 70% of README texts classified as positive tone
- **Technical Ecosystems**: Clear separation into Python, Java, and C/C++ dominated communities

## Project Structure

```
Github-Graph/
├── ProjectNotebook.ipynb    # Main analysis notebook
├── README.md                # This file
├── repo_data.json           # Repository metadata (generated)
├── github_graph.gexf        # Network graph (generated)
├── user_languages.json      # Language mappings (generated)
└── github_graph.log         # Execution logs (generated)
```

## Data Collection Notes

- **BFS Sampling**: The breadth-first approach creates a densely sampled local neighborhood rather than a representative sample of all GitHub
- **Rate Limits**: GitHub API has rate limits (5,000 requests/hour with authentication); the tool handles this by saving progress
- **Bot Filtering**: Accounts with "[bot]" in username are automatically excluded
- **Language Normalization**: JVM languages (Java, Scala, Kotlin) are grouped; C and C++ are combined

## License

This project is for research and educational purposes.

## Citation

If you use this tool in your research, please consider citing the underlying methodology and acknowledging the use of the GitHub API.
