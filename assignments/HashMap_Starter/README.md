# Introduction
Assignment #2 for CS106L, get resource from the [official website](http://web.stanford.edu/class/cs106l/assignment2.html). This assignment is to mark the `const` for class member functions and write four special member functions(copy constructor, copy assignment, move constructor and move assignment) for the class.

# Main Points
## Mark Const
- We need to mark a member function with `const` when the function doesn't modify the member data(can use `mutable` key word to make some member data can be modified by function marked with `const`).       
    ```c++
    #include <iostream>
    class TestClass{
    private:
        int member_;
        mutable int const_change_; //function with const can modify this

    public:
        TestClass():member_(0),const_change_(0){}

        /*
        * const object call const version, none const object call none const version
        */
        void testfunc() const{ 
            //member_ ++; //error
            const_change_ ++; //right
            std::cout<< "don't modify the normal data: "<<member_<<", modify the mutable data: "<< const_change_<<std::endl;
        }
        void testfunc() {
            member_ ++;
            const_change_ ++; //right
            std::cout<< " modify the data: "<<member_<<", "<<const_change_<<std::endl;
        }


    };

    int main(){
        TestClass tc;
        const TestClass const_tc;
        tc.testfunc(); //call the none const
        const_tc.testfunc(); //call const
        return 0;
    }   

    ```
    **const object call const version, none const object call none const version.**
- We can return the member data with `const` when we don't want to modify the returned data.
    ```c++
    #include <iostream>
    #include<string>

    class MyClass{
    public:
        MyClass():name("hello world!"){}

        std::string& getName(){
            std::cout<<"call no const"<<std::endl;
            return name; 
        }

        const std::string& getName() const{ //usually use static_cast and none const version to realize the const version.
            std::cout<<"call  const"<<std::endl;
            return static_cast<const std::string &>(const_cast<MyClass*>(this)->getName());
        }


    private:
        std::string name;

    };

    int main(){
        MyClass c;
        const MyClass cc;
        const std::string &c_name = c.getName();
        std::string &c_name1 = c.getName();
        const std::string &cc_name = cc.getName();
        std::cout<<"before modify: "<<c.getName()<<std::endl;

        //c_name[0] = 'm'; // can't modify
        c_name1[0] = 'm'; // can modify
        //cc_name[0] = 'm';
        
        std::cout<< "after modify: "<<c_name1<<" "<<c_name<<std::endl; //c_name1 and c_name is same

        
        return 0; 
    }
    ```
    output:
    ```
    call no const
    call no const
    call  const
    call no const
    before modify: call no const
    hello world!
    after modify: mello world! mello world!
    ```

## Special Member Function
- there are six special member functions.   
    ```c++
    class MyClass{
    public:
        MyClass(); //default constructor
        ~MyClass(); //destructor
        MyClass(const MyClass &); //copy constructor
        MyClass& operator = (const MyClass &); //copy assignment constructor
        MyClass(const MyClass &&); //move constructor
        MyClass& operator = (const MyClass &&); //move assignment 
    }

    ```
- copy constructor and copy assignment need to copy all member data.
    ```c++
    template <typename K, typename M, typename H>
    HashMap<K, M, H>::HashMap(const HashMap &hp):_size(hp._size),_hash_function(hp._hash_function),_buckets_array(hp._buckets_array){
        //traverse the array and copy all node
        for(size_t i=0; i<_buckets_array.size(); ++i){
            
            
            if(_buckets_array[i]){
                _buckets_array[i] = new node(_buckets_array[i]->value, _buckets_array[i]->next);
                node *temp_pointer = _buckets_array[i];
                while(temp_pointer->next){
                    temp_pointer->next = new node(temp_pointer->next->value, temp_pointer->next->next);
                    temp_pointer = temp_pointer->next;
                }

            }
        }


    }

    template <typename K, typename M, typename H>
    HashMap<K, M, H>& HashMap<K,M,H>::operator = (const HashMap &hp){
        if(&hp == this) return *this;
        clear(); //remove all data
        _size = hp._size;
        _hash_function = hp._hash_function;
        _buckets_array = hp._buckets_array;
        for(size_t i=0; i<_buckets_array.size(); ++i){
            
            
            if(_buckets_array[i]){
                _buckets_array[i] = new node(_buckets_array[i]->value, _buckets_array[i]->next);
                node *temp_pointer = _buckets_array[i];
                while(temp_pointer->next){
                    temp_pointer->next = new node(temp_pointer->next->value, temp_pointer->next->next);
                    temp_pointer = temp_pointer->next;
                }

            }
        }
        return *this;

    }
    ```
- move constructor and move assignment need to use `std::move()` to return a r-value from l-value.
    ```c++
    template <typename K, typename M, typename H>
    HashMap<K,M,H>::HashMap (HashMap &&hp){
        _size = std::move(hp._size);
        _hash_function = std::move(hp._hash_function);
        _buckets_array = std::move(hp._buckets_array);

    }

    template <typename K, typename M, typename H>
    HashMap<K, M, H>& HashMap<K,M,H>::operator = (HashMap &&hp){
        if(&hp==this) return *this;
        clear();
        _size = std::move(hp._size);
        _hash_function = std::move(hp._hash_function);
        _buckets_array = std::move(hp._buckets_array);

        return *this;
    }

    ```
- two assignment functions need to do self judgement.
    ```c++
    if(&hp==this) return *this;
    ```
- two assignment functions need to erase current resources.
    ```c++
    clear(); //in above functions
    ```

