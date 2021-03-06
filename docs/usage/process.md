---
template: overrides/main.html
---

# process

## extract

Find the best matches in a list of choices.

=== "Python"

    Parameters:

    - **query**: *str*

        String we want to find.

    - **choices**: *Iterable*

        List of all strings the query should be compared with or dict with a mapping
        in the form of `{<result>: <string to compare>}`. Mapping can be anything
        that provides an `items` method like a python `dict` or `pandas.Series` (index: element)

    - **scorer**: *Callable*, default `fuzz.WRatio`

        Optional callable that is used to calculate the matching score between
        the query and each choice.

    - **processor**: *Callable*, default `utils.default_process`

        Optional callable that reformats the strings. `utils.default_process`
        is used by default, which lowercases the strings and trims whitespace

    - **limit**: *int*

        Maximum amount of results to return.

    - **score_cutoff**: *float*, default `0`

        Optional argument for a score threshold. Matches with
        a lower score than this number will not be returned.

    Returns:

    - **matches**: *List[Tuple[str, float]] or List[Tuple[str, float, str]])*

        Returns a list of all matches that have a `score >= score_cutoff`. The list will
        be of either `(, )` when `choices` is a list of strings or `(, , )` when `choices` is a
        mapping.


    ```console
    > choices = ["Atlanta Falcons", "New York Jets", "New York Giants", "Dallas Cowboys"]
    > process.extract("new york jets", choices, limit=2)
    [('new york jets', 100), ('new york giants', 78.57142639160156)]
    ```

=== "C++"
    ```cpp
    #include "process.hpp"
    using rapidfuzz::process::extract;

    // matches is a vector of std::pairs
    // [('new york jets', 100), ('new york giants', 78.57142639160156)]
    auto matches = extract(
      "new york jets",
      std::vector<std::string>{"Atlanta Falcons", "New York Jets", "New York Giants", "Dallas Cowboys"},
      utils::default_process<char>,
      fuzz::ratio<std::string, std::string>
      2);
    ```

## extractOne
Finds the best match in a list of choices by comparing them using the provided scorer functions.

=== "Python"

    Parameters: Same as [extract](#extract)

    Returns:

    - **matches**: *Optional[Tuple[str, float]]*

        Returns the best match in form of a tuple or None when there is
        no match with a `score >= score_cutoff`.


    ```console
    > choices = ["Atlanta Falcons", "New York Jets", "New York Giants", "Dallas Cowboys"]
    > process.extractOne("cowboys", choices)
    ("dallas cowboys", 90)
    ```

=== "C++"
    ```cpp
    #include "process.hpp"
    using rapidfuzz::process::extractOne;

    // matches is a boost::optional<std::pair>
    // ("dallas cowboys", 90)
    auto matches = extractOne(
      "cowboys",
      std::vector<std::string>{"Atlanta Falcons", "New York Jets", "New York Giants", "Dallas Cowboys"});
    ```
