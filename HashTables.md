# Hash Tables

## Overview
This submission implements a hash table in C++ that stores string keys and int values using separate chaining with vector<list<pair<string,int>>>. 
It tracks collisions, computes load factor, supports search and remove, and dynamically resizes by rehashing when the load factor exceeds 0.75.

## HashTable Implementation (Separate Chaining, Collision Counting, Rehashing)
```cpp
#include <iostream>
#include <vector>
#include <list>
#include <string>
#include <utility>
#include <stdexcept>
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
        int oldCapacity = capacity;
        vector<list<pair<string, int>>> oldTable = table;

        capacity = capacity * 2;
        table.clear();
        table.resize(capacity);

        currentSize = 0;
        collisionCount = 0;

        for (int i = 0; i < oldCapacity; i++) {
            for (const auto& kv : oldTable[i]) {
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
            if (kv.first == key) return kv.second;
        }
        return -1;
    }

    double loadFactor() const {
        if (capacity == 0) return 0.0;
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
            int bsz = static_cast<int>(bucket.size());
            if (bsz > mx) mx = bsz;
        }
        return mx;
    }

    double avgBucketLenNonEmpty() const {
        int nonEmpty = 0;
        for (const auto& bucket : table) {
            if (!bucket.empty()) nonEmpty++;
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
    cout << "Max bucket size: " << ht.maxBucketSize() << "\n";
    cout << "Avg bucket length (non empty): " << fixed << setprecision(6) << ht.avgBucketLenNonEmpty() << "\n";
}

int main() {
    cout << "Part 5: Basic functionality test\n";

    HashTable ht;

    vector<string> words;
    words.reserve(120);
    for (int i = 0; i < 120; i++) {
        words.push_back("word" + to_string(i));
    }

    for (int i = 0; i < (int)words.size(); i++) {
        ht.insert(words[i], i * 10);
    }

    cout << "Capacity: " << ht.getCapacity() << "\n";
    cout << "Elements: " << ht.size() << "\n";
    cout << "Load factor: " << fixed << setprecision(6) << ht.loadFactor() << "\n";
    cout << "Total collisions: " << ht.getCollisionCount() << "\n";

    string existingKey = "word50";
    string missingKey = "not_in_table";

    cout << "Search existing key " << existingKey << ": " << ht.search(existingKey) << "\n";
    cout << "Search missing key " << missingKey << ": " << ht.search(missingKey) << "\n";

    cout << "Remove word10: " << (ht.remove("word10") ? "true" : "false") << "\n";
    cout << "Remove word11: " << (ht.remove("word11") ? "true" : "false") << "\n";
    cout << "Search removed word10: " << ht.search("word10") << "\n";

    cout << "\nPart 6: Experimental analysis\n";

    mt19937 rng(42);

    vector<string> randomKeys;
    randomKeys.reserve(200);
    for (int i = 0; i < 200; i++) {
        randomKeys.push_back(randomString(10, rng));
    }

    vector<string> sequentialKeys;
    sequentialKeys.reserve(200);
    for (int i = 1; i <= 200; i++) {
        sequentialKeys.push_back("student" + to_string(i));
    }

    vector<string> samePrefixKeys;
    samePrefixKeys.reserve(200);
    for (int i = 1; i <= 200; i++) {
        samePrefixKeys.push_back("data_" + paddedNumber(i, 4));
    }

    runExperiment("Random strings", randomKeys);
    runExperiment("Sequential keys: student1..student200", sequentialKeys);
    runExperiment("Same prefix keys: data_0001..data_0200", samePrefixKeys);

    return 0;
}
```

## Experimental Analysis
Random strings usually distribute more evenly across buckets because their characters vary earlier and more unpredictably, so the polynomial rolling hash tends to spread keys out. 
That typically shows up as fewer collisions, a smaller maximum bucket size, and a lower average chain length among non empty buckets compared to more structured key sets.

Sequential keys and same prefix keys often create more clustering because many keys share long common prefixes, so their hash computations are similar for most of the loop and only diverge near the end. 
In practice that can increase collisions and produce a few heavier buckets, especially before resizing. Resizing helps because it reduces load factor and gives more buckets to distribute into, 
but it does not fully fix poor key diversity if the hash and key patterns still align in an unlucky way.
