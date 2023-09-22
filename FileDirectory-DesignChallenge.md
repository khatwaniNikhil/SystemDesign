# Given 
1. File: path, size
2. Directories: Collection<File> no nested hierarchy

# Expected
Report: Total size of all files: lookup all files mapped to directories as well as unmapped
TopK Collections by size: sorted by size collection of directories(TreeSet)
