# Trie (Prefix Tree)

**Usage:** Autocomplete, Spell Checker, Prefix matching.
**Complexity:** Insert/Search O(L) where L is key length.

## Implementation

```python
class TrieNode:
    def __init__(self):
        self.children = {} # char -> TrieNode
        self.is_word = False

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word):
        node = self.root
        for char in word:
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]
        node.is_word = True

    def search(self, word):
        node = self.root
        for char in word:
            if char not in node.children:
                return False
            node = node.children[char]
        return node.is_word

    def startsWith(self, prefix):
        node = self.root
        for char in prefix:
            if char not in node.children:
                return False
            node = node.children[char]
        return True
```

## Variation: Counting Words / Passing Through

_Store logic in TrieNode_

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.count = 0 # Number of words passing through this node
```
