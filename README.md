**Hackerank**

typedef struct {
    int u, v, w;
} Edge;

int parent[3005];

int findParent(int x) {
    if (parent[x] == x)
        return x;

    return parent[x] = findParent(parent[x]);
}

void unionSet(int a, int b) {
    a = findParent(a);
    b = findParent(b);

    if (a != b)
        parent[a] = b;
}

int compare(const void *a, const void *b) {
    Edge *e1 = (Edge *)a;
    Edge *e2 = (Edge *)b;

    if (e1->w != e2->w)
        return e1->w - e2->w;

    return (e1->u + e1->v) - (e2->u + e2->v);
}

int kruskals(int g_nodes, int g_edges, int* g_from, int* g_to, int* g_weight) {

    Edge edges[g_edges];

    for (int i = 0; i < g_edges; i++) {
        edges[i].u = g_from[i];
        edges[i].v = g_to[i];
        edges[i].w = g_weight[i];
    }

    qsort(edges, g_edges, sizeof(Edge), compare);

    for (int i = 1; i <= g_nodes; i++) {
        parent[i] = i;
    }

    int mstCost = 0;

    for (int i = 0; i < g_edges; i++) {

        int u = edges[i].u;
        int v = edges[i].v;
        int w = edges[i].w;

        if (findParent(u) != findParent(v)) {

            mstCost += w;

            unionSet(u, v);
        }
    }

    return mstCost;
}

**C**

#include <assert.h>
#include <ctype.h>
#include <limits.h>
#include <math.h>
#include <stdbool.h>
#include <stddef.h>
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

char* readline();
char* ltrim(char*);
char* rtrim(char*);
char** split_string(char*);

int parse_int(char*);

typedef struct {
    int u, v, w;
} Edge;

int parent[3005];

int findParent(int x) {
    if (parent[x] == x)
        return x;

    return parent[x] = findParent(parent[x]);
}

void unionSet(int a, int b) {
    a = findParent(a);
    b = findParent(b);

    if (a != b)
        parent[a] = b;
}

int compare(const void *a, const void *b) {
    Edge *e1 = (Edge *)a;
    Edge *e2 = (Edge *)b;

    if (e1->w != e2->w)
        return e1->w - e2->w;

    return (e1->u + e1->v) - (e2->u + e2->v);
}

int kruskals(int g_nodes, int g_edges, int* g_from, int* g_to, int* g_weight) {

    Edge edges[g_edges];

    for (int i = 0; i < g_edges; i++) {
        edges[i].u = g_from[i];
        edges[i].v = g_to[i];
        edges[i].w = g_weight[i];
    }

    qsort(edges, g_edges, sizeof(Edge), compare);

    for (int i = 1; i <= g_nodes; i++) {
        parent[i] = i;
    }

    int mstCost = 0;

    for (int i = 0; i < g_edges; i++) {

        int u = edges[i].u;
        int v = edges[i].v;
        int w = edges[i].w;

        if (findParent(u) != findParent(v)) {

            mstCost += w;

            unionSet(u, v);
        }
    }

    return mstCost;
}
int main()
{
    FILE* fptr = fopen(getenv("OUTPUT_PATH"), "w");

    char** g_nodes_edges = split_string(rtrim(readline()));

    int g_nodes = parse_int(*(g_nodes_edges + 0));
    int g_edges = parse_int(*(g_nodes_edges + 1));

    int* g_from = malloc(g_edges * sizeof(int));
    int* g_to = malloc(g_edges * sizeof(int));
    int* g_weight = malloc(g_edges * sizeof(int));

    for (int i = 0; i < g_edges; i++) {
        char** g_from_to_weight = split_string(ltrim(readline()));

        int g_from_temp = parse_int(*(g_from_to_weight + 0));
        int g_to_temp = parse_int(*(g_from_to_weight + 1));
        int g_weight_temp = parse_int(*(g_from_to_weight + 2));

        *(g_from + i) = g_from_temp;
        *(g_to + i) = g_to_temp;
        *(g_weight + i) = g_weight_temp;
    }

    int res = kruskals(g_nodes, g_edges, g_from, g_to, g_weight);

    fprintf(fptr, "%d\n", res);

    fclose(fptr);

    return 0;
}

char* readline() {
    size_t alloc_length = 1024;
    size_t data_length = 0;

    char* data = malloc(alloc_length);

    while (true) {
        char* cursor = data + data_length;
        char* line = fgets(cursor, alloc_length - data_length, stdin);

        if (!line) {
            break;
        }

        data_length += strlen(cursor);

        if (data_length < alloc_length - 1 || data[data_length - 1] == '\n') {
            break;
        }

        alloc_length <<= 1;

        data = realloc(data, alloc_length);

        if (!data) {
            data = '\0';

            break;
        }
    }

    if (data[data_length - 1] == '\n') {
        data[data_length - 1] = '\0';

        data = realloc(data, data_length);

        if (!data) {
            data = '\0';
        }
    } else {
        data = realloc(data, data_length + 1);

        if (!data) {
            data = '\0';
        } else {
            data[data_length] = '\0';
        }
    }

    return data;
}

char* ltrim(char* str) {
    if (!str) {
        return '\0';
    }

    if (!*str) {
        return str;
    }

    while (*str != '\0' && isspace(*str)) {
        str++;
    }

    return str;
}

char* rtrim(char* str) {
    if (!str) {
        return '\0';
    }

    if (!*str) {
        return str;
    }

    char* end = str + strlen(str) - 1;

    while (end >= str && isspace(*end)) {
        end--;
    }

    *(end + 1) = '\0';

    return str;
}

char** split_string(char* str) {
    char** splits = NULL;
    char* token = strtok(str, " ");

    int spaces = 0;

    while (token) {
        splits = realloc(splits, sizeof(char*) * ++spaces);

        if (!splits) {
            return splits;
        }

        splits[spaces - 1] = token;

        token = strtok(NULL, " ");
    }

    return splits;
}

int parse_int(char* str) {
    char* endptr;
    int value = strtol(str, &endptr, 10);

    if (endptr == str || *endptr != '\0') {
        exit(EXIT_FAILURE);
    }

    return value;
}
