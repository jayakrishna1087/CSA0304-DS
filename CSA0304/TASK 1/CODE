/*
 * Q1 - Library catalogue as a Binary Search Tree (key = accession number)
 * Usage: ./q1_bst_full [section]   section = 1..5 (no argument runs all)
 *   1 basic operations + 4 traversals     2 deletion (leaf / one child / two children)
 *   3 save (preorder) and rebuild         4 empty tree + duplicate keys
 *   5 experiment: 2000 keys, random vs sorted insertion
 */
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <limits.h>

typedef struct Node { int key; struct Node *left, *right; } Node;

/* ---------------- core operations ---------------- */
static Node *new_node(int key)
{
    Node *n = malloc(sizeof *n);
    if (!n) { perror("malloc"); exit(1); }
    n->key = key; n->left = n->right = NULL;
    return n;
}

/* iterative insert: no recursion depth problem for degenerate trees; duplicates ignored */
Node *insert(Node *root, int key, int *added)
{
    *added = 0;
    Node **p = &root;
    while (*p) {
        if (key == (*p)->key) return root;           /* duplicate */
        p = key < (*p)->key ? &(*p)->left : &(*p)->right;
    }
    *p = new_node(key);
    *added = 1;
    return root;
}

int search(const Node *r, int key, int *cmp)         /* returns 1 if found, counts comparisons */
{
    *cmp = 0;
    while (r) {
        (*cmp)++;
        if (key == r->key) return 1;
        r = key < r->key ? r->left : r->right;
    }
    return 0;
}

int height(const Node *r)                            /* empty tree = -1 */
{
    if (!r) return -1;
    int hl = height(r->left), hr = height(r->right);
    return 1 + (hl > hr ? hl : hr);
}

int count(const Node *r) { return r ? 1 + count(r->left) + count(r->right) : 0; }

/* delete - three cases: leaf, one child, two children (inorder successor) */
static const char *last_case = "not found";
Node *delete_key(Node *t, int key)
{
    if (!t) return NULL;
    if (key < t->key)      t->left  = delete_key(t->left, key);
    else if (key > t->key) t->right = delete_key(t->right, key);
    else if (t->left && t->right) {                  /* case 3: two children */
        Node *s = t->right;
        while (s->left) s = s->left;                 /* inorder successor = min of right subtree */
        t->key = s->key;                             /* copy successor's key up */
        t->right = delete_key(t->right, s->key);     /* delete the successor (case 1 or 2) */
        last_case = "case 3 (two children, replaced by inorder successor)";
        return t;
    } else {                                         /* case 1 or 2 */
        Node *c = t->left ? t->left : t->right;
        last_case = c ? "case 2 (one child, child takes its place)" : "case 1 (leaf, simply removed)";
        free(t);
        return c;
    }
    return t;
}

void destroy(Node *r) { if (r) { destroy(r->left); destroy(r->right); free(r); } }

/* ---------------- traversals ---------------- */
void preorder(const Node *r)  { if (r) { printf("%d ", r->key); preorder(r->left); preorder(r->right); } }
void inorder(const Node *r)   { if (r) { inorder(r->left); printf("%d ", r->key); inorder(r->right); } }
void postorder(const Node *r) { if (r) { postorder(r->left); postorder(r->right); printf("%d ", r->key); } }
void levelorder(const Node *r)
{
    if (!r) return;
    int n = count(r), head = 0, tail = 0;
    const Node **q = malloc(n * sizeof *q);
    q[tail++] = r;
    while (head < tail) {
        const Node *x = q[head++];
        printf("%d ", x->key);
        if (x->left)  q[tail++] = x->left;
        if (x->right) q[tail++] = x->right;
    }
    free(q);
}
void show_all(const Node *r)
{
    printf("  preorder   : "); preorder(r);   printf("\n");
    printf("  inorder    : "); inorder(r);    printf("\n");
    printf("  postorder  : "); postorder(r);  printf("\n");
    printf("  levelorder : "); levelorder(r); printf("\n");
}

/* bracket form: key(left,right) - used for verification and for the report figures */
void bracket(const Node *r)
{
    if (!r) return;
    printf("%d", r->key);
    if (r->left || r->right) {
        printf("("); bracket(r->left); printf(","); bracket(r->right); printf(")");
    }
}

/* BST property check */
int is_bst(const Node *r, long lo, long hi)
{
    if (!r) return 1;
    if (r->key <= lo || r->key >= hi) return 0;
    return is_bst(r->left, lo, r->key) && is_bst(r->right, r->key, hi);
}
#define VERIFY(t) (is_bst((t), LONG_MIN, LONG_MAX) ? "valid BST" : "INVALID!")

/* ---------------- file save / rebuild ---------------- */
static void save_rec(const Node *r, FILE *f) { if (r) { fprintf(f, "%d\n", r->key); save_rec(r->left, f); save_rec(r->right, f); } }
int save_tree(const Node *r, const char *name)       /* PREORDER: parent before children */
{
    FILE *f = fopen(name, "w");
    if (!f) return 0;
    save_rec(r, f);
    fclose(f);
    return 1;
}
Node *load_tree(const char *name)
{
    FILE *f = fopen(name, "r");
    if (!f) return NULL;
    Node *t = NULL; int k, added;
    while (fscanf(f, "%d", &k) == 1) t = insert(t, k, &added);
    fclose(f);
    return t;
}
int same_shape(const Node *a, const Node *b)
{
    if (!a || !b) return a == b;
    return a->key == b->key && same_shape(a->left, b->left) && same_shape(a->right, b->right);
}

/* ---------------- sections ---------------- */
static int build_demo(Node **t)
{
    int keys[] = {50, 30, 70, 20, 40, 60, 80, 65, 35}, added;
    for (int i = 0; i < 9; i++) *t = insert(*t, keys[i], &added);
    return 9;
}

static void section1(void)
{
    printf("=== 1. Basic operations and traversals ===\n");
    Node *t = NULL;
    build_demo(&t);
    printf("Inserted: 50 30 70 20 40 60 80 65 35\n");
    printf("Tree (bracket form): "); bracket(t); printf("\n");
    show_all(t);
    printf("Height = %d, nodes = %d\n", height(t), count(t));
    int c;
    int keys[] = {65, 35, 99};
    for (int i = 0; i < 3; i++) {
        int f = search(t, keys[i], &c);
        printf("search(%d) -> %s, comparisons = %d\n", keys[i], f ? "FOUND" : "NOT FOUND", c);
    }
    destroy(t);
}

static void section2(void)
{
    printf("=== 2. Deletion - all three cases ===\n");
    Node *t = NULL;
    build_demo(&t);
    printf("Start        : "); bracket(t); printf("   [%s]\n", VERIFY(t));
    int del[] = {20, 40, 50};
    const char *what[] = {"leaf", "node with one child", "node with two children (root)"};
    for (int i = 0; i < 3; i++) {
        t = delete_key(t, del[i]);
        printf("delete %-3d (%s)\n", del[i], what[i]);
        printf("  -> %s\n", last_case);
        printf("  tree : "); bracket(t); printf("\n");
        printf("  inorder: "); inorder(t); printf("  [%s]\n", VERIFY(t));
    }
    destroy(t);
}

static void section3(void)
{
    printf("=== 3. Save with PREORDER and rebuild ===\n");
    Node *t = NULL;
    build_demo(&t);
    save_tree(t, "tree_preorder.txt");
    Node *u = load_tree("tree_preorder.txt");
    printf("original : "); bracket(t); printf("\n");
    printf("rebuilt  : "); bracket(u); printf("\n");
    printf("identical shape: %s\n", same_shape(t, u) ? "YES" : "NO");
    /* what would happen with inorder? */
    Node *v = NULL; int added;
    int sorted[] = {20, 30, 35, 40, 50, 60, 65, 70, 80};
    for (int i = 0; i < 9; i++) v = insert(v, sorted[i], &added);
    printf("rebuilt from INORDER file: height %d (degenerate) vs original height %d\n", height(v), height(t));
    destroy(t); destroy(u); destroy(v);
}

static void section4(void)
{
    printf("=== 4. Empty tree and duplicate keys ===\n");
    Node *t = NULL; int c, added;
    printf("empty tree: height = %d, nodes = %d\n", height(t), count(t));
    printf("empty tree: search(10) -> %s, comparisons = %d\n", search(t, 10, &c) ? "FOUND" : "NOT FOUND", c);
    printf("empty tree: inorder -> [");  inorder(t); printf("]\n");
    t = delete_key(t, 10);
    printf("empty tree: delete(10) -> still %s\n", t == NULL ? "empty (no crash)" : "non-empty?");
    int keys[] = {40, 20, 60, 20, 40, 60, 20};
    int inserted = 0;
    for (int i = 0; i < 7; i++) { t = insert(t, keys[i], &added); inserted += added; }
    printf("inserted 7 keys {40 20 60 20 40 60 20}: stored %d distinct nodes\n", inserted);
    printf("inorder -> "); inorder(t); printf("\n");
    destroy(t);
}

static unsigned long long rng = 88172645463325252ULL;
static unsigned next_rand(void) { rng ^= rng << 13; rng ^= rng >> 7; rng ^= rng << 17; return (unsigned)(rng >> 11); }

static void section5(void)
{
    printf("=== 5. Experiment: 2000 accession numbers ===\n");
    enum { N = 2000 };
    int keys[N], added;
    for (int i = 0; i < N; i++) keys[i] = 100001 + i;       /* accession numbers in increasing order */

    Node *sorted_t = NULL;
    for (int i = 0; i < N; i++) sorted_t = insert(sorted_t, keys[i], &added);

    int rnd[N]; memcpy(rnd, keys, sizeof keys);
    for (int i = N - 1; i > 0; i--) { int j = next_rand() % (i + 1); int x = rnd[i]; rnd[i] = rnd[j]; rnd[j] = x; }
    Node *random_t = NULL;
    for (int i = 0; i < N; i++) random_t = insert(random_t, rnd[i], &added);

    long tot_r = 0, tot_s = 0; int max_r = 0, max_s = 0, c;
    for (int i = 0; i < N; i++) {
        search(random_t, keys[i], &c); tot_r += c; if (c > max_r) max_r = c;
        search(sorted_t, keys[i], &c); tot_s += c; if (c > max_s) max_s = c;
    }
    printf("%-28s %12s %12s\n", "", "random order", "sorted order");
    printf("%-28s %12d %12d\n", "nodes", count(random_t), count(sorted_t));
    printf("%-28s %12d %12d\n", "height", height(random_t), height(sorted_t));
    printf("%-28s %12.2f %12.2f\n", "avg comparisons / search", (double)tot_r / N, (double)tot_s / N);
    printf("%-28s %12d %12d\n", "max comparisons", max_r, max_s);
    printf("%-28s %12s %12s\n", "BST property", VERIFY(random_t), VERIFY(sorted_t));
    printf("ideal (balanced) height = floor(log2 2000) = 10, worst = n-1 = 1999\n");
    destroy(random_t); destroy(sorted_t);
}

int main(int argc, char **argv)
{
    int sel = argc > 1 ? atoi(argv[1]) : 0;
    if (!sel || sel == 1) section1();
    if (!sel || sel == 2) section2();
    if (!sel || sel == 3) section3();
    if (!sel || sel == 4) section4();
    if (!sel || sel == 5) section5();
    return 0;
}
