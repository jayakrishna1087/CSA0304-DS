#include <stdio.h>
#include <stdlib.h>

typedef struct Trie {
    struct Trie *child[26];
    int words;                  /* number of words passing through here */
    int end;                    /* 1 if a word ends here */
} Trie;

static int nodes_created = 0;
Trie *node(void) { nodes_created++; return calloc(1, sizeof(Trie)); }

void insert(Trie *t, const char *w)
{
    /* TODO 1 (completed): for every lowercase letter create the child if missing,
       move to it and increase its words count; mark end at the last node */
    const Trie *start = t;
    for (; *w >= 'a' && *w <= 'z'; w++) {
        int i = *w - 'a';
        if (!t->child[i]) t->child[i] = node();
        t = t->child[i];
        t->words++;
    }
    if (t != start) t->end = 1;
}

int count_prefix(const Trie *t, const char *p)
{
    /* TODO 2 (completed): follow the letters of p */
    for (; *p; p++) {
        int i = *p - 'a';
        if (i < 0 || i >= 26 || !t->child[i]) return 0;
        t = t->child[i];
    }
    return t->words;
}

void destroy(Trie *t)
{
    if (!t) return;
    for (int i = 0; i < 26; i++) destroy(t->child[i]);
    free(t);
}

int main(void)
{
    const char *contacts[] = {"anand", "anitha", "anu", "arun", "bala", "anusuya"};
    Trie *root = node();
    for (int i = 0; i < 6; i++) insert(root, contacts[i]);
    const char *typed[] = {"a", "an", "anu", "b", "z"};
    for (int i = 0; i < 5; i++)
        printf("%s:%d ", typed[i], count_prefix(root, typed[i]));
    printf("\n");
    printf("nodes created = %d (including the root), memory = %d x %zu bytes = %zu bytes\n",
           nodes_created, nodes_created, sizeof(Trie), nodes_created * sizeof(Trie));
    destroy(root);
    return 0;
}
