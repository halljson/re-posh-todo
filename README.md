# libs this project uses

- [Datascript](https://github.com/tonsky/datascript)
- [Posh](https://github.com/mpdairy/posh): allows the declarative binding of user interface components to a local DataScript database.
- [Re-frame](https://github.com/Day8/re-frame): is a pattern for writing SPAs in CLJS, using [Reagent (aka. react)](http://reagent-project.github.io/) and an event dispatch/subscribe mechanism that make it possible to manage state-manipulating user actions. See aforementioned [buzzwords statement here](https://github.com/Day8/re-frame#why-should-you-care).
- [Re-posh](https://github.com/denistakeda/re-posh): "a library that empowers you to improve your re-frame single-page applications to have a robust and flexible in-page database to manage your application's state"

- Noteworthy excerpts; 
- Re-frame innards are [just a loop](https://github.com/Day8/re-frame#it-is-a-loop)
- [Re-frame development workflow](https://github.com/Day8/re-frame#so-your-job-is-) - only stand to improve this with re-posh

### (TLDR);

```cljs

;; this is the view from like... pluto

(def datascript (cljs.magically/browserify :datomic)) 
  
(def posh (+ :ui-components :datascript))
  
(def re-frame (+ :cljs :buzzwords :react))

(def re-posh (+ :posh :re-frame))
```


# Todo

This is a [TodoMVC-esque](https://github.com/tastejs/todomvc) application, forked from [Posh-todo](https://github.com/mpdairy/posh-todo). The intent of this project is to replace Posh-todo's use of [Posh](https://github.com/mpdairy/posh) with [Re-Posh](https://github.com/denistakeda/re-posh). Hence the name ... my, how clever I am after work these days.


~~This Todo app lets you add or delete tasks to different categories and view by checked/unchecked or by category.~~

~~There's no styling and it's a really lame todo list, but I made it just to show an example of how to use Posh.~~

* (strikethrough only tempory, until development is stable)

~~You can see posh-todo in action here: http://otherway.org/posh-todo/~~

;; TODO I'll try to get a demo up as soon as I can get it working ;).

## Usage

Clone it, then

```
lein run -m clojure.main script/figwheel.clj
```

Then go here in your browser:

```
http://localhost:3449/
```
## Some nice components

### Checkbox

If you have an entity with a boolean value that you want the user to
be able to change, just load this component with the `id` and `attr`
of the entity.

```clj
(defn checkbox [conn id attr checked?]
  [:input
      {:type "checkbox"
       :checked checked?
       :onChange #(p/transact! conn [[:db/add id attr (not checked?)]])}])
```

The component above would be called from another component that loads
the entity and supplies the `checked?` value of the `attr`.

If you wanted a standalone checkbox, you could query within the
component:

```clj
(defn checkbox [conn id attr]
  (let [checked? (attr @(p/pull conn [attr] id))]
    [:input
     {:type "checkbox"
      :checked checked?
      :onChange #(p/transact! conn [[:db/add id attr (not checked?)]])}]))
```

### Add Box

This component loads a text input with an add button that calls the
callback function with the value of the text box  whenever the add
button is clicked. It uses a local reagent atom to update the text
box.

```clj
(defn add-box [add-fn]
  (let [edit (r/atom "")]
    (fn [add-fn]
      [:span
       [:input
        {:type "text"
         :value @edit
         :onChange #(reset! edit (-> % .-target .-value))}]
       [:button
        {:onClick #(when-not (empty? @edit)
                     (add-fn @edit)
                     (reset! edit ""))}
                     (or (:button-text options) "Add")]])))
```

The add-fn would be something like `(partial add-task! conn
category-id)` where `add-task!` is:

```clj
(defn add-task!
  [conn category-id task-name]
  (util/new-entity! conn {:task/name task-name
                          :task/category category-id
                          :task/done false}))
```

This adds a new task to a category in the todo.


## License

Copyright © 2015 Matt Parker

Distributed under the Eclipse Public License either version 1.0 or (at
your option) any later version.
