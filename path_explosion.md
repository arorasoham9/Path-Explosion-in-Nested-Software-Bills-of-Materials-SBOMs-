# Path Explosion in Nested Software Bills of Materials (SBOMs)

## Abstract

The Software Bill of Materials (SBOM) is a critical component in modern software supply chain management, providing a comprehensive list of the components and dependencies within a software product. As software systems become increasingly complex, SBOMs can become nested, leading to a path explosion problem in the SBOM structure where the number of paths to be analyzed grows exponentially. This paper explores the path explosion problem associated with nested SBOMs, describes the implications for comparison and analysis, and presents strategies and solutions to manage this complexity effectively.

## Introduction

In the evolving landscape of software development, managing the software supply chain has become increasingly complex due to the intricate dependencies and components used across different software products. An SBOM provides detailed information about these components, including their versions, licenses, and vulnerabilities. However, when SBOMs reference other SBOMs, creating a nested structure, the number of paths to be analyzed can grow exponentially. This paper investigates the path explosion problem caused by nested SBOMs, illustrating the challenges with examples, and proposes methods to mitigate the problem.

## Path Explosion Problem

### 1. Definition and Impact

Path explosion refers to the rapid increase in the number of paths that must be analyzed when dealing with nested SBOMs. Each top-level SBOM may reference multiple nested SBOMs, creating a combinatorial increase in the number of paths. This phenomenon has significant implications for the efficiency and effectiveness of SBOM analysis and comparison.

### 2. Example Scenario

Consider a scenario with two top-level SBOMs, A1 and A2, each referencing two nested SBOMs, B1 and B2. The resulting paths are:

- **A1xB1**
- **A1xB2**
- **A2xB1**
- **A2xB2**

Here, each top-level SBOM (A1 and A2) results in two paths for the nested SBOMs. Comparing all possible pairs involves:

- **A1xB1** vs. **A1xB2**
- **A1xB1** vs. **A2xB1**
- **A1xB1** vs. **A2xB2**
- **A1xB2** vs. **A2xB1**
- **A1xB2** vs. **A2xB2**
- **A2xB1** vs. **A2xB2**

With four paths, this results in six unique pairwise comparisons.

### 3. Increasing Complexity

Extending this example to three top-level SBOMs (A1, A2, A3), each referencing three nested SBOMs (B1, B2, B3), the number of paths becomes:

- **A1xB1**, **A1xB2**, **A1xB3**
- **A2xB1**, **A2xB2**, **A2xB3**
- **A3xB1**, **A3xB2**, **A3xB3**

This results in a total of nine unique paths. The number of pairwise comparisons increases to:

$\frac{9 \times (9 - 1)}{2} = 36$

Such an increase in comparisons can lead to significant computational overhead, especially when each comparison involves detailed analysis of the SBOM contents.

## Strategies for Managing Path Explosion

### 1. Path Aggregation and Comparison Algorithm

**Objective:** Reduce the number of paths to compare by aggregating similar paths based on their common attributes.

### Detailed Description:

- **Extract Paths:** Traverse the SBOMs to retrieve paths that represent various component dependencies.
- **Group Paths:** Use a map or dictionary to group paths by their nested SBOM references. Paths that refer to the same nested SBOM are placed in the same group.
- **Compare Groups:** Within each group of paths, compare only the paths within that group. This reduces the number of comparisons since paths across different groups are less likely to be directly comparable.

### Example:

Given paths A1xB1, A1xB2, A2xB1, and A2xB2, group paths by their nested SBOM:

- **Group 1:** B1 -> A1xB1, A2xB1
- **Group 2:** B2 -> A1xB2, A2xB2

Compare paths within each group:

- Compare A1xB1 with A2xB1
- Compare A1xB2 with A2xB2
### Drawbacks of the Algorithm

1. **Over-Simplification:** 
   - May miss relevant comparisons by focusing only on common attributes.

2. **Inflexibility:** 
   - Struggles with paths that don't fit neatly into groups or span multiple groups.

3. **Complex Grouping:** 
   - Grouping process can be complex and computationally expensive with large SBOMs.

4. **Redundant Comparisons:** 
   - Grouping may lead to unnecessary comparisons within a group.
5. **Handling Dynamic Changes:** 
   - Frequent SBOM updates require re-grouping, adding overhead.
6. **Scalability Issues:** 
   - Effectiveness decreases with growing number or depth of SBOMs.
7. **Loss of Context:** 
   - Important relationships between paths in different groups may be overlooked.


## 2. Difference Minimization Algorithm

**Objective:** Find the minimal set of differences required for comparison, focusing on the most significant changes.

### Detailed Description:

- **Identify Key Differences:** Implement a function to assess differences between two paths. This function evaluates variations in path attributes (e.g., top-level SBOM, nested SBOM).
- **Select Minimal Differences:** Based on the differences, select the most significant ones to minimize the number of comparisons. This can be achieved by focusing on differences that impact key attributes or components.

### Example:

For paths A1xB1, A1xB2, A2xB1, and A2xB2, compute differences:

- Differences between A1xB1 and A2xB1
- Differences between A1xB2 and A2xB2

### Drawbacks of the Algorithm

1. **Over-Simplification:** 
   - Focusing on minimal differences may overlook subtle but important variations, reducing the comprehensiveness of the comparison.

2. **Subjectivity in Selection:** 
   - Determining which differences are "most significant" can be subjective, leading to inconsistent or biased results.

3. **Increased Complexity:** 
   - Identifying and selecting minimal but significant differences may require complex logic, potentially increasing computational overhead.

4. **Context Loss:** 
   - By narrowing down differences, the algorithm may lose important contextual information, affecting the accuracy of the comparison.

5. **Scalability Issues:** 
   - As the number of paths or the complexity of differences grows, the algorithm may struggle to efficiently handle and prioritize differences.

6. **Difficulty in Automation:** 
   - Automating the selection of minimal differences might be challenging, especially in dynamic environments with frequently changing SBOMs.

## 3. Redundancy Removal Algorithm
**Objective:** The algorithm aims to identify and remove common subgraphs from two directed graphs, reducing redundancy and ensuring only unique subgraphs remain.

## Steps

### 1. Leaf Node Identification
- **Task**: Identify leaf nodes (nodes with no outgoing edges) in both graphs.
- **Action**: Traverse each graph and collect nodes that do not have any successors.

### 2. Predecessor Identification
- **Task**: For any given node, identify its predecessors (nodes with edges pointing to it).
- **Action**: Traverse the adjacency map of the graph to find all nodes that have edges pointing to the given node.

### 3. Subgraph Comparison and Removal
- **Task**: Identify and prepare to remove common subgraphs from both graphs.
- **Action**: 
  - Start with the smaller set of leaf nodes to optimize the process.
  - For each common leaf node in both graphs, recursively check its predecessors.
  - Ensure that all predecessors are identical in both graphs to confirm that the subgraph is common.
  - Collect nodes that form these common subgraphs.

### 4. Subgraph Removal
- **Task**: Remove the identified common subgraphs from both graphs.
- **Action**: 
  - Iterate through the list of nodes forming common subgraphs and remove them from both graphs.
  - Repeat the process until no more common subgraphs can be found.
### Drawbacks of the Algorithm

1. **Over-Simplification:** 
   - The algorithm might overlook significant non-leaf nodes that contribute to redundancy, potentially missing important subgraphs.

2. **Increased Complexity:** 
   - Identifying and comparing subgraphs can become computationally expensive, especially with large or complex graphs.

3. **Context Loss:** 
   - Removing common subgraphs may lead to a loss of important contextual information, which could affect the integrity of the remaining graph structure.

4. **Scalability Issues:** 
   - As the size of the graphs or the number of nodes increases, the algorithm may struggle to efficiently handle the complexity, leading to performance bottlenecks.

5. **Potential for Incomplete Redundancy Removal:** 
   - The recursive approach might not identify all redundant subgraphs, particularly in cases with complex or interdependent subgraphs.

6. **Difficulty in Handling Dynamic Changes:** 
   - If graphs are frequently updated, the algorithm may need to be re-run, introducing overhead and potentially leading to inconsistencies.
<!-- 
## 4. Longest Common Subsequence (LCS) Algorithm

**Objective:** Find the longest common subsequence between two sequences. This helps in identifying the similarities between two sequences of paths, which can be useful for comparison and reduction of path explosion.

### Detailed Steps:

1. **Define the Problem:**
   Given two sequences \( A \) and \( B \), the goal is to find the longest subsequence that is common to both. A subsequence is a sequence derived by deleting some or no elements of the original sequence without changing the order of the remaining elements.

2. **Create a DP Table:**
   Construct a 2D DP table `dp` where `dp[i][j]` represents the length of the LCS of the first \( i \) elements of sequence \( A \) and the first \( j \) elements of sequence \( B \).

3. **Initialize the DP Table:**
   If either sequence is empty, the LCS length is 0. Hence, initialize the first row and first column of the DP table to 0.

4. **Fill the DP Table:**
   For each element in sequences \( A \) and \( B \), fill the DP table based on whether the elements match or not:
   - If `( A[i-1] == B[j-1] )`, then `dp[i][j] = dp[i-1][j-1] + 1` because the current element is part of the LCS.
   - Otherwise, `dp[i][j] = \max(dp[i-1][j], dp[i][j-1])` because the current element is not part of the LCS, and we need to take the maximum LCS length found so far.

5. **Retrieve the Result:**
   The value at `dp[m][n]` (where \( m \) and \( n \) are the lengths of sequences \( A \) and \( B \), respectively) gives the length of the LCS.

### Example:

Consider sequences:
- \( A = [A1, A2, A3, A4] \)
- \( B = [A2, A4, A1] \)

**Steps:**

1. **Initialize the DP Table:**
   Create a 5x4 table (since lengths are 4 and 3 plus one row/column for the empty subsequence).

2. **Fill the Table:**
   Compare \( A1 \) with each element of \( B \) and fill based on matches and previous values in the table.

**Result:**
The length of the LCS will be the value in the bottom-right cell of the table.

## 5. Edit Distance Algorithm

**Objective:** Compute the minimum number of operations (insertions, deletions, substitutions) required to transform one sequence into another. This is useful to measure the similarity between two paths or sequences of paths.

### Detailed Steps:

1. **Define the Problem:**
   Given two sequences \( A \) and \( B \), determine the minimum number of operations required to convert sequence \( A \) into sequence \( B \). The operations are:
   - Insertion
   - Deletion
   - Substitution

2. **Create a DP Table:**
   Construct a 2D DP table `dp` where `dp[i][j]` represents the minimum edit distance between the first \( i \) elements of sequence \( A \) and the first \( j \) elements of sequence \( B \).

3. **Initialize the DP Table:**
   - `dp[i][0]` represents the distance of transforming the first \( i \) elements of \( A \) into an empty sequence, which is \( i \) (all deletions).
   - `dp[0][j]` represents the distance of transforming an empty sequence into the first \( j \) elements of \( B \), which is \( j \) (all insertions).

4. **Fill the DP Table:**
   For each pair of indices \((i, j)\), calculate `dp[i][j]` based on whether the elements match or not:
   - If \( A[i-1] == B[j-1] \), then `dp[i][j] = dp[i-1][j-1]` (no operation needed).
   - Otherwise, compute the cost as `1 + \min(dp[i-1][j], dp[i][j-1], dp[i-1][j-1])`, representing:
     - Deletion: `dp[i-1][j] + 1`
     - Insertion: `dp[i][j-1] + 1`
     - Substitution: `dp[i-1][j-1] + 1`

5. **Retrieve the Result:**
   The value at `dp[m][n]` (where \( m \) and \( n \) are the lengths of sequences \( A \) and \( B \), respectively) gives the minimum edit distance.

### Example:

Consider sequences:
- \( A = [A1, A2, A3] \)
- \( B = [A1, A3, A4] \)

**Steps:**

1. **Initialize the DP Table:**
   Create a 4x4 table (since lengths are 3 and 3 plus one row/column for the empty sequence).

2. **Fill the Table:**
   Compare each element and fill the table based on the minimum number of operations.

**Result:**
The value in the bottom-right cell of the table gives the minimum edit distance.
 -->
## Conclusion
As SBOMs become increasingly nested, the number of paths to be analyzed grows exponentially, leading to substantial computational overhead.

**Path Explosion Impact:** The combinatorial increase in paths when SBOMs reference other SBOMs can lead to a vast number of pairwise comparisons. This growth not only complicates the analysis but also strains computational resources.

**Algorithms for Mitigation:** 
   - **Path Aggregation and Comparison Algorithm:** By grouping similar paths based on common attributes and comparing within groups, we can significantly reduce the number of necessary comparisons.
   - **Difference Minimization Algorithm:** Identifying and focusing on key differences helps in minimizing the number of comparisons, thus making the process more efficient.

- **Graph Redundancy Elimination Algorithm**: Identifying and recursively removing common subgraphs from two directed graphs to reduce redundancy and maintain unique graph structures, ensuring efficient comparison and analysis.
<!-- 
   - **Longest Common Subsequence (LCS) Algorithm:** This dynamic programming approach helps in identifying similarities between sequences of paths, providing insights into their commonalities.
   - **Edit Distance Algorithm:** Computing the minimum number of operations required to transform one sequence into another allows for measuring the similarity between sequences and assessing differences effectively.
 -->
The application of these algorithms not only aids in managing the path explosion problem but also enhances the efficiency of SBOM analysis and comparison. 


## References

1. **ISO/IEC 20246:2020** - Information technology — Software Bill of Materials (SBOM).
2. **NIST Special Publication 800-53** - Security and Privacy Controls for Information Systems and Organizations.
3. **Software Package Data Exchange (SPDX) Specification** - A standard format for SBOMs.
