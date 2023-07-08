# Introduction
Assignment #1 for CS106L, get resource from the [official website](http://web.stanford.edu/class/cs106l/assignment1.html)

# Main Points
- use `std::search` to find a substring in the total string.
    ```c++
    //find string delim in (url_start, end] of a string, all parameters and return value are iterator
    url_start = std::search(url_start,end,delim.begin(),delim.end());
    ```
- use `std::find` to find a char in the string.
    ```c++
    // find `"` in (url_start+delim.size(), end]  
    auto url_end = std::find(url_start+delim.size(), end, "\""); 
    ```
- use two iterators to construct a substring from a string.
    ```c++
    //get the substring in range (url_start+delim.size(), url_end]
    string link(url_start+delim.size(),url_end); 
    ```
- use `std::all_of` to seach the total container.
    ```c++
    // return true when all elements in link obey the lambda relu 
    return std::all_of(link.begin(),link.end(),[](char a){return a!='#' && a!=':';});
    ```
- use `std::priority_queue` and lambda expression to define customized priority queue.
    ```c++
    using container = vector<vector<string>>;
    auto cmp_fn = [&w, &target_set](const vector<string>& left, const vector<string>& right) {
            string page1 = left.back();
            string page2 = right.back();
            int page1_number = numCommonLinks(w.getLinkSet(page1), target_set);
            int page2_number = numCommonLinks(w.getLinkSet(page2), target_set);
            return page1_number<page2_number;
        };
    std::priority_queue<vector<string>, container, decltype(cmp_fn)> queue(cmp_fn); //use decltype and construct queue with the compare function
    ``` 