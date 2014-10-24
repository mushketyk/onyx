## Functions

This section outlines how Onyx programs execute behavior. Onyx uses plain Clojure functions to carry out distributed activity. You have the option of performing aggregation before each function.

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [Functions](#functions)
  - [Transformation](#transformation)
  - [Grouping & Aggregation](#grouping-&-aggregation)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

#### Transformation

A Transformer is a function that takes a segment as a parameter and outputs a segment or a seq of segments. Transformers are meant to literally transform a single unit of data in a functional manner. The following is an example of a transformer:

```clojure
(defn my-inc [{:keys [n] :as segment}]
  (assoc segment :n (inc n)))
```

#### Grouping & Aggregation

Grouping means consolidates "like" values together to the same virtual peer to presumably compute an aggregate. Grouping is specified inside of a catalog entry. There are two ways to two group: by key of segment, or by arbitrary function. Grouping by key is a convenience that will reach into each segment and pin all segments with the same key value in the segment together. Grouping functions receive a single segment as input. The output of a grouping function is the value to group on. If a virtual peer receiving grouped segments fails, all remaining segments of the group will be pinned to another virtual peer.

To group by a key in a segment, use `:onyx/group-by-key` in the catalog entry:

```clojure
{:onyx/name :sum-balance
 :onyx/ident :onyx.peer.kw-grouping-test/sum-balance
 :onyx/fn :onyx.peer.kw-grouping-test/sum-balance
 :onyx/type :transformer
 :onyx/group-by-key :name
 :onyx/consumption :concurrent
 :onyx/batch-size 1000}
```

To group by an arbitrary function, use `:onyx/group-by-fn` in the catalog entry:

```clojure
{:onyx/name :sum-balance
 :onyx/ident :onyx.peer.fn-grouping-test/sum-balance
 :onyx/fn :onyx.peer.fn-grouping-test/sum-balance
 :onyx/type :transformer
 :onyx/group-by-fn :onyx.peer.fn-grouping-test/group-by-name
 :onyx/consumption :concurrent
 :onyx/batch-size 1000}
```
