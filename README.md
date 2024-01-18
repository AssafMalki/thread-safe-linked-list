# thread-safe-linked-list

This repository contains a C language implementation of a thread-safe linked list. The design of the API emphasizes ease of use, reliability, and functionality. The data within the linked list is encapsulated as a `void *` type. The internal structure of the linked list is abstracted from the user, preventing direct interaction with nodes. However, the mutex associated with each linked list is accessible, offering the user the ability to manipulate it if necessary.

## Installation

Execute the following command:

```bash
$ git clone https://github.com/AssafMalki/thread-safe-linked-list.git && cd ./thread-safe-linked-list/ && make o && cd ..
```

This command clones the project and compiles the object code in the `thread-safe-linked-list/obj/` directory. The resulting object code can subsequently be linked as needed.

Using `make exec` compiles the executable that contains the test suites.

## Examples

## API

The features accessible to the user are thoroughly documented in `include/ll.h`.

### Data Structure

The core data structure of this project is a straightforward singly-linked list, reinforced with a reader/writer mutex for concurrency control. The structure includes methods to print the linked list and to clean up elements.

```c
// linked list
struct ll {
    // current length
    int len;

    // pointer to the initial node
    ll_node_t *hd;

    // mutex for ensuring thread safety
    pthread_rwlock_t m;

    // function invoked each time a value is removed,
    // receives a pointer to that value
    gen_fun_t val_teardown;

    // function for printing the values in a linked list
    gen_fun_t val_printer;
};
```

### Functions

```c
// creates and returns a pointer to a new linked list.
// requires a teardown function to be called with
// a pointer to the value during its deletion.
ll_t *ll_new(gen_fun_t val_teardown);

// iterates through the linked list, deallocating everything (including `list`)
void ll_delete(ll_t *list);

// adds a value to the linked list at position `n`. Acceptable values for n range from `0`
// (inserts at the beginning) to `list->len` (inserts at the end).
// returns the updated length of the linked list if successful, -1 otherwise
int ll_insert_n(ll_t *list, void *val, int n);

// inserts a value at the start of the linked list.
// returns the updated length of the linked list if successful, -1 otherwise
int ll_insert_first(ll_t *list, void *val);

// appends a value at the end of the linked list.
// returns the updated length of the linked list if successful, -1 otherwise
int ll_insert_last(ll_t *list, void *val);

// removes the value at position n from the linked list.
// returns the updated length of the linked list if successful, -1 otherwise
int ll_remove_n(ll_t *list, int n);

// removes the first value from the linked list.
// returns the updated length of the linked list if successful, -1 otherwise
int ll_remove_first(ll_t *list);

// removes the first element in the linked list that satisfies the provided condition.
// returns the updated length of the linked list if successful, -1 otherwise
int ll_remove_search(ll_t *list, int cond(void *));

// retrieves a pointer to the `n`th value in the linked list.
// returns `NULL` if unsuccessful
void *ll_get_n(ll_t *list, int n);

// retrieves a pointer to the first value in the linked list.
// returns `NULL` if the list is empty
void *ll_get_first(ll_t *list);

// applies function f to all values in the list
void ll_map(ll_t *list, gen_fun_t f);

// iterates through the linked list values and invokes `list->val_printer` on each
void ll_print(ll_t list);

// a generic teardown function for values that require no specific action
void ll_no_teardown(void *n);
```

## Testing

Run the following command to execute tests:

```bash
$ make test
```