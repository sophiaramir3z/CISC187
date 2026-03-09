# Hash Tables

## Part 1: Class Structure
This lab implements a hash table in C++ that stores string keys and int values using separate chaining with vector<list<pair<string, int>>>. The implementation supports insertion, search, removal, load factor calculation, collision counting, and dynamic resizing through rehashing.

The class uses the following internal data members:
- table: stores buckets as lists of key value pairs
- currentSize: tracks the number of elements currently stored
- capacity: tracks the number of buckets
- collisionCount: tracks how many times a new key is inserted into a non empty bucket

## Part 2: Hash Function
The hash table uses a polynomial rolling hash function:
```cpp
int hashFunction(const string& key) const {
    const int prime = 31;
    long long hash = 0;

    for (char c : key) {
        hash = hash * prime + static_cast<unsigned char>(c);
    }

    long long idx = hash % capacity;
    if (idx < 0) idx += capacity;
    return static_cast<int>(idx);
}
```
This function processes each character in the string and builds a hash value using base 31. The final index is computed using modulo by the table capacity.

## Part 3: Insert and Collision Handling
Insertion uses separate chaining. Each bucket is a linked list of key value pairs. If a key already exists, its value is updated instead of inserting a duplicate.
A collision is counted whenever a new key is inserted into a bucket that is already non empty.

## Part 4: Resizing and Rehashing
When the load factor exceeds 0.75, the table is resized by doubling the capacity. All existing key value pairs are then reinserted into the new table using the updated capacity.
During rehashing:
- the old table is copied
- the new table is allocated with double capacity
- currentSize and collisionCount are reset
- all elements are reinserted into the new table
This ensures the table maintains efficiency as more elements are added.

## HashTable Implementation
```cpp
#include <iostream>
#include <vector>
#include <list>
#include <string>
#include <utility>
#include <iomanip>
#include <random>

using namespace std;

class HashTable {
private:
    vector<list<pair<string, int>>> table;
    int currentSize;
    int capacity;
    int collisionCount;

    int hashFunction(const string& key) const {
        const int prime = 31;
        long long hash = 0;

        for (char c : key) {
            hash = hash * prime + static_cast<unsigned char>(c);
        }

        long long idx = hash % capacity;
        if (idx < 0) idx += capacity;
        return static_cast<int>(idx);
    }

    void insertNoRehash(const string& key, int value) {
        int idx = hashFunction(key);
        auto& bucket = table[idx];

        for (auto& kv : bucket) {
            if (kv.first == key) {
                kv.second = value;
                return;
            }
        }

        if (!bucket.empty()) {
            collisionCount++;
        }

        bucket.push_back({key, value});
        currentSize++;
    }

    void rehash() {
        vector<list<pair<string, int>>> oldTable = table;

        capacity *= 2;
        table.clear();
        table.resize(capacity);

        currentSize = 0;
        collisionCount = 0;

        for (const auto& bucket : oldTable) {
            for (const auto& kv : bucket) {
                insertNoRehash(kv.first, kv.second);
            }
        }
    }

public:
    HashTable(int size = 11)
        : table(size), currentSize(0), capacity(size), collisionCount(0) {}

    void insert(const string& key, int value) {
        insertNoRehash(key, value);

        if (loadFactor() > 0.75) {
            rehash();
        }
    }

    bool remove(const string& key) {
        int idx = hashFunction(key);
        auto& bucket = table[idx];

        for (auto it = bucket.begin(); it != bucket.end(); ++it) {
            if (it->first == key) {
                bucket.erase(it);
                currentSize--;
                return true;
            }
        }
        return false;
    }

    int search(const string& key) const {
        int idx = hashFunction(key);
        const auto& bucket = table[idx];

        for (const auto& kv : bucket) {
            if (kv.first == key) {
                return kv.second;
            }
        }
        return -1;
    }

    double loadFactor() const {
        return static_cast<double>(currentSize) / static_cast<double>(capacity);
    }

    int size() const {
        return currentSize;
    }

    bool isEmpty() const {
        return currentSize == 0;
    }

    int getCapacity() const {
        return capacity;
    }

    int getCollisionCount() const {
        return collisionCount;
    }

    int maxBucketSize() const {
        int mx = 0;
        for (const auto& bucket : table) {
            if ((int)bucket.size() > mx) {
                mx = bucket.size();
            }
        }
        return mx;
    }

    double avgBucketLengthAll() const {
        return static_cast<double>(currentSize) / static_cast<double>(capacity);
    }

    double avgBucketLengthNonEmpty() const {
        int nonEmpty = 0;
        for (const auto& bucket : table) {
            if (!bucket.empty()) {
                nonEmpty++;
            }
        }
        if (nonEmpty == 0) return 0.0;
        return static_cast<double>(currentSize) / static_cast<double>(nonEmpty);
    }

    void printTable() const {
        for (int i = 0; i < capacity; i++) {
            cout << i << ": ";
            for (const auto& kv : table[i]) {
                cout << "(" << kv.first << ", " << kv.second << ") ";
            }
            cout << "\n";
        }
    }
};

static string randomString(int len, mt19937& rng) {
    uniform_int_distribution<int> dist('a', 'z');
    string s;
    s.reserve(len);
    for (int i = 0; i < len; i++) {
        s.push_back(static_cast<char>(dist(rng)));
    }
    return s;
}

static string paddedNumber(int x, int width) {
    string t = to_string(x);
    if ((int)t.size() >= width) return t;
    return string(width - (int)t.size(), '0') + t;
}

static void runExperiment(const string& name, const vector<string>& keys) {
    HashTable ht;

    for (int i = 0; i < (int)keys.size(); i++) {
        ht.insert(keys[i], i);
    }

    cout << "\nExperiment: " << name << "\n";
    cout << "Capacity: " << ht.getCapacity() << "\n";
    cout << "Elements: " << ht.size() << "\n";
    cout << "Load factor: " << fixed << setprecision(6) << ht.loadFactor() << "\n";
    cout << "Total collisions: " << ht.getCollisionCount() << "\n";
    cout << "Maximum bucket size: " << ht.maxBucketSize() << "\n";
    cout << "Average bucket length (all buckets): " << ht.avgBucketLengthAll() << "\n";
    cout << "Average bucket length (non empty buckets): " << ht.avgBucketLengthNonEmpty() << "\n";
}

int main() {
    cout << "Part 5: Testing Program\n";

    HashTable ht;

    vector<string> words;
    words.reserve(120);
    for (int i = 0; i < 120; i++) {
        words.push_back("word" + to_string(i));
    }

    for (int i = 0; i < (int)words.size(); i++) {
        ht.insert(words[i], i * 10);
    }

    cout << "Table capacity: " << ht.getCapacity() << "\n";
    cout << "Number of elements: " << ht.size() << "\n";
    cout << "Load factor: " << fixed << setprecision(6) << ht.loadFactor() << "\n";
    cout << "Total collisions: " << ht.getCollisionCount() << "\n";

    cout << "Search existing key word50: " << ht.search("word50") << "\n";
    cout << "Search non existing key not_in_table: " << ht.search("not_in_table") << "\n";

    cout << "Remove word10: " << (ht.remove("word10") ? "true" : "false") << "\n";
    cout << "Remove word11: " << (ht.remove("word11") ? "true" : "false") << "\n";
    cout << "Search removed key word10: " << ht.search("word10") << "\n";

    cout << "\nPart 6: Experimental Analysis\n";

    mt19937 rng(42);

    vector<string> randomKeys;
    for (int i = 0; i < 200; i++) {
        randomKeys.push_back(randomString(10, rng));
    }

    vector<string> sequentialKeys;
    for (int i = 1; i <= 200; i++) {
        sequentialKeys.push_back("student" + to_string(i));
    }

    vector<string> samePrefixKeys;
    for (int i = 1; i <= 200; i++) {
        samePrefixKeys.push_back("data_" + paddedNumber(i, 4));
    }

    runExperiment("Random strings", randomKeys);
    runExperiment("Sequential keys", sequentialKeys);
    runExperiment("Same prefix keys", samePrefixKeys);

    return 0;
}
```
## Part 5: Testing Program
The testing program inserts 120 words, which satisfies the requirement of inserting at least 100 words. It then prints the table capacity, number of elements, load factor, and total collisions. It also searches for one existing key and one non existing key, removes two keys, and verifies correctness by searching for a removed key afterward.

## Part 6: Experimental Analysis
The random strings generally produce the best distribution because the characters vary more unpredictably across the full key. As a result, the hash values are usually spread more evenly across buckets, which tends to reduce collisions and keep bucket sizes smaller. This usually leads to a lower maximum bucket size and a lower average bucket length compared with more patterned inputs.

The sequential and same prefix keys often produce more clustering because many keys share most of their characters. In both cases, the hash values differ only near the end of the string, so the keys can land in similar buckets more often. That tends to increase collisions and create heavier chains in some buckets. Rehashing helps by lowering the load factor and distributing elements across more buckets, but it cannot completely eliminate clustering caused by similar key patterns.



