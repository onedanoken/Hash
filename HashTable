#include <iostream>
#include <vector>
#include <string>
#include <algorithm>
#include <cassert>

constexpr auto A = 29;
constexpr auto index_rehash = 0.75;

unsigned long long HashHorner(const std::string& key, unsigned long long table_size) {
    int hash = 0;
    for (int i = 0; i < key.size(); i++)
        hash += hash * A + key[i];
    return hash % table_size;
}

class HashTable {
public:
    explicit HashTable(int table_size) : table(table_size), isDeleted(table_size) {}

    bool Add(const std::string& Key);
    bool Remove(const std::string& key);
    bool Has(const std::string& key) const;

private:
    std::vector<std::string> table;
    std::vector<bool> isDeleted;
    unsigned long long elementsCount = 0;
    unsigned long long deletedCount = 0;

    class CIterator {
    public:
        explicit CIterator(int startHash, int size) : startHash(startHash), size(size), i(0)  {}

        bool Move() {
            if (i == size - 1)
                return false;
            else {
                i++;
                return true;
            }
        }

        unsigned long long GetHash() {
            return (startHash + (i  + i * i) / 2) % size;
        }
            
    private:
        unsigned long long startHash;
        unsigned long long size;
        unsigned long long i;
    };

    bool needRehash() const {
        return static_cast<double>(elementsCount + deletedCount) / table.size() >= index_rehash;
    }

    void Rehash() {
        HashTable newTable(2 * table.size());
        for (unsigned long long key = 0; key < table.size(); key++) {
            if (!table[key].empty())
                newTable.Add(table[key]);
        }
        *this = std::move(newTable);
    }
};

bool HashTable::Has(const std::string& key) const {
    assert(!key.empty());
    CIterator It(HashHorner(key, table.size()), table.size());
    do {
        unsigned long long hash = It.GetHash();
        if (!isDeleted[hash]) {
            if (table[hash].empty())
                return false;
            else if (table[hash] == key)
                return true;
        }
        else
            return false;
    } while (It.Move());
    assert(false);
    return false;
}

bool HashTable::Add(const std::string& key) {
    assert(!key.empty());
	if (needRehash())
        Rehash();
	
    CIterator It(HashHorner(key, table.size()), table.size());
    do {
        unsigned long long hash = It.GetHash();
        if (table[hash].empty()) {
            table[hash] = key;
            if (isDeleted[hash]) {
                isDeleted[hash] = false;
                deletedCount--;
            }
            elementsCount++;
            return true;
        }
        else if (table[hash] == key)
            return false;
          
    } while (It.Move());  
}

bool HashTable::Remove(const std::string& key) {
    assert(!key.empty());
    CIterator It(HashHorner(key, table.size()), table.size());
    do {
        unsigned long long hash = It.GetHash();
        if (table[hash].empty() && !isDeleted[hash]) {
            return false;
        }
        else if (table[hash] == key) {
            table[hash] = std::string();
            isDeleted[hash] = true;
            deletedCount++;
            elementsCount--;
            return true;
        }

    } while (It.Move());
    assert(false);
    return false;
}

int main() {
    std::ios_base::sync_with_stdio(false);
    std::cin.tie(NULL);
    std::cout.tie(NULL);
    HashTable table(8);
    char command;
    std::string value;
    while (std::cin >> command >> value) {
        if (command == '+') {
            std::cout << (table.Add(value) ? "OK" : "FAIL") << std::endl;
        }
        else if (command == '?') {
            std::cout << (table.Has(value) ? "OK" : "FAIL") << std::endl;
        }
        else if (command == '-') {
            std::cout << (table.Remove(value) ? "OK" : "FAIL") << std::endl;
        }
    }
    return 0;
}

