# Lab 6 - Scientific Computing

## Plot Words

### Part 1
- Code:  
```
    import gzip
    from string import ascii_lowercase as lowercase
    
    import networkx as nx    
    
    def generate_graph(words):
        G = nx.Graph(name="words")
        lookup = dict((c, lowercase.index(c)) for c in lowercase)
    
        def edit_distance_one(word):
            for i in range(len(word)):
                left, c, right = word[0:i], word[i], word[i + 1:]
                j = lookup[c]  # lowercase.index(c)
                for cc in lowercase[j + 1:]:
                    yield left + cc + right
        candgen = ((word, cand) for word in sorted(words)
                   for cand in edit_distance_one(word) if cand in words)
        G.add_nodes_from(words)
        for word, cand in candgen:
            G.add_edge(word, cand)
        return G
    
    
    def words_graph():
        """Return the words example graph from the Stanford GraphBase"""
        fh = gzip.open('words_dat.txt.gz', 'r')
        words = set()
        for line in fh.readlines():
            line = line.decode()
            if line.startswith('*'):
                continue
            w = str(line[0:5])
            words.add(w)
        return generate_graph(words)
    
    
    if __name__ == '__main__':
        G = words_graph()
        print("Loaded words_dat.txt containing 5757 five-letter English words.")
        print("Two words are connected if they differ in one letter.")
        print("Graph has %d nodes with %d edges"
              % (nx.number_of_nodes(G), nx.number_of_edges(G)))
        print("%d connected components" % nx.number_connected_components(G))
    
        for (source, target) in [('chaos', 'order'),
                                 ('plots', 'graph'),
                                 ('moron', 'smart'),
                                 ('flies', 'swims'),
                                 ('mango', 'peach'),
                                 ('pound', 'marks')]:
            print("Shortest path between %s and %s is" % (source, target))
            try:
                sp = nx.shortest_path(G, source, target)
                for n in sp:
                    print(n)
            except nx.NetworkXNoPath:
                print("None")
```

- Results:
```
Loaded words_dat.txt containing 5757 five-letter English words.
Two words are connected if they differ in one letter.
Graph has 5757 nodes with 14135 edges
853 connected components
Shortest path between chaos and order is
chaos
choos
shoos
shoes
shoed
shred
sired
sided
aided
added
adder
odder
order
Shortest path between plots and graph is
plots
plats
plate
prate
grate
grape
graph
Shortest path between moron and smart is
moron
boron
baron
caron
capon
capos
capes
canes
banes
bands
bends
beads
bears
sears
stars
start
smart
Shortest path between flies and swims is
flies
flips
slips
slims
swims
Shortest path between mango and peach is
mango
mange
marge
merge
merse
terse
tease
pease
peace
peach
Shortest path between pound and marks is
None
```

### Part 2

- Code:
```
import gzip
from string import ascii_lowercase as lowercase

import networkx as nx

#-------------------------------------------------------------------
#   The Words/Ladder graph of Section 1.1
#-------------------------------------------------------------------


def generate_graph(words):
    G = nx.Graph(name="words")
    lookup = dict((c, lowercase.index(c)) for c in lowercase)

    def edit_distance_one(word):
        for i in range(len(word)):
            left, c, right = word[0:i], word[i], word[i + 1:]
            j = lookup[c]  # lowercase.index(c)
            for cc in lowercase[j + 1:]:
                yield left + cc + right
    candgen = ((word, cand) for word in sorted(words)
               for cand in edit_distance_one(word) if cand in words)
    G.add_nodes_from(words)
    for word, cand in candgen:
        G.add_edge(word, cand)
    return G


def words_graph():
    """Return the words example graph from the Stanford GraphBase"""
    fh = gzip.open('words4_dat.txt.gz', 'r')
    words = set()
    for line in fh.readlines():
        line = line.decode()
        if line.startswith('*'):
            continue
        w = str(line[0:4])
        words.add(w)
    return generate_graph(words)


if __name__ == '__main__':
    G = words_graph()
    print("Graph has %d nodes with %d edges"
          % (nx.number_of_nodes(G), nx.number_of_edges(G)))
    print("%d connected components" % nx.number_connected_components(G))

    for (source, target) in [('cold', 'warm'),
                             ('love', 'hate'),
                             ('good', 'evil'),
                             ('pear', 'beef'),
                             ('make', 'take')]:
        print("Shortest path between %s and %s is" % (source, target))
        try:
            sp = nx.shortest_path(G, source, target)
            for n in sp:
                print(n)
        except nx.NetworkXNoPath:
            print("None")
```

- Results:
```
Graph has 2174 nodes with 8040 edges
129 connected components
Shortest path between cold and warm is
cold
wold
word
ward
warm
Shortest path between love and hate is
love
hove
have
hate
Shortest path between good and evil is
None
Shortest path between pear and beef is
pear
bear
beer
beef
Shortest path between make and take is
make
take
```

### Part 3

- Code for edit_distance_one():
```
    def edit_distance_one(word):
        for i in range(len(word)):
            left, c, right = word[0:i], word[i], word[i + 1:]
            j = lookup[c]  # lowercase.index(c)
            for cc in lowercase[j + 1:]:
                for p in permutations(left + cc + right):
                    yield "".join(p)
```

- Results:
Graph has 5757 nodes with 112278 edges
16 connected components
Shortest path between chaos and order is
chaos
hoars
arose
adore
order
Shortest path between plots and graph is
plots
opals
plash
sharp
graph
Shortest path between moron and smart is
moron
manor
roams
smart
Shortest path between flies and swims is
flies
isles
semis
swims
Shortest path between mango and peach is
mango
conga
nacho
poach
peach
Shortest path between pound and marks is
pound
mound
monad
damns
drams
marks