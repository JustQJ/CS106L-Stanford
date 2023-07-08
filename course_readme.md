# 基本情况
参考链接：https://csdiy.wiki/%E7%BC%96%E7%A8%8B%E5%85%A5%E9%97%A8/CS106L/
课程网站：http://web.stanford.edu/class/cs106l/
课程视频：https://www.youtube.com/channel/UCSqr6y-eaQT_qZJVUm_4QxQ/playlists
课程教材：http://web.stanford.edu/class/cs106l/full_course_reader.pdf
Assignment1: WikiRacer：http://web.stanford.edu/class/cs106l/assignment1.html
Assignment2: STL HashMap：http://web.stanford.edu/class/cs106l/assignment2.html


# 学习小要点
- C++是静态语言（statically typed），需要提前编译，所以在每个函数、变量等符号前都要声明类型。
- 所有的变量最好在定义的同时初始化，结构体使用`{}`符号。
- 函数的区分是通过函数名和形参，所以对于重载函数（同名函数），只能通过参数进行区分。但下面的声明比较特殊，需要注意，第二个函数是无法被正常调用的。
    ```c++
    int divide(int a, int b=2){
        std::cout<< "divide function 1"<<std::endl;
        return a/b;
    }

    int divide(int a){ 
        std::cout<<"divide function 2"<<std::endl;
        return a/2;
    }

    ``` 
- `auto`可以进行自动推断类型，一般用于一些类型名很长的情况（例如`std::pair<std::string, std::unordered_map>`就可以使用`auto`代替），但是最好不要用于类型名很短的情况（例如`int`这种基础类型）。`auto`是在编译器阶段推断类型，所以依旧是静态类型，不是动态的。
- 结构体的赋值(`=`)操作都是复制一份，需要使用引用`&`才会引用原来的对象。下面的代码说明自定义的结构体和stl中的都是这样。
    ```c++
    struct Student{
        int age;
        int grade;
    };


    int main(){

        std::vector<int> original_v{1,2};
        std::vector<int> copy_v = original_v; //赋值会导致自动复制一份 
        std::vector<int> &ref_v = original_v; 
        original_v[0] = -1;
        copy_v[0] = 2; //不会影响原来的结果
        ref_v[0] = 1;

        std::cout<<original_v[0]<<std::endl;
        std::cout<<copy_v[0]<<std::endl; 
        std::cout<<ref_v[0]<<std::endl;


        struct Student original_s{1,2};
        struct Student copy_s = original_s; //自己构造的结构体赋值也会导致自动复制一份 
        struct Student &ref_s = original_s; 
        original_s.age = -1;
        copy_s.age = 2; //不会影响原来的结果
        ref_s.age = 1;

        std::cout<<original_s.age<<std::endl;
        std::cout<<copy_s.age<<std::endl; 
        std::cout<<ref_s.age<<std::endl;

        return 0;

    }
    ```
    **需要注意下面的错误**：
    ```c++
    void shift(vector<std::pair<int, int>>& nums) {
        for (size_t i = 0; i < nums.size(); ++i) {
            auto [num1, num2] = nums[i]; //这里是复制了一份新的，所以原来的并没有改变
            num1++;
            num2++;
        }
    }
    //修正为
    void shift(vector<std::pair<int, int>>& nums) {
        for (size_t i = 0; i < nums.size(); ++i) {
            auto& [num1, num2] = nums[i]; //这里才是引用原来的元素
            num1++;
            num2++;
        }
    }
    ```
- 左值和右值，左值可以在`=`左边或者右边，右值只能在右边。左值可以被引用，而右值不能，这在函数传递时容易出错。
    ```c++
    int &ref_rvalue = 5; //这是不行的，因为5是右值

    void shift(vector<std::pair<int, int>>& nums) {
        for (auto& [num1, num2]: nums) {
        num1++;
        num2++;
        }
    }

    shift({{1,2}}); //这也是不行的，因为函数里面进行了引用
    ```
    
- 非`const`是不能引用`const`的变量的。
    ```c++
    const std::vector<int> c_vec{7, 8};
    std::vector<int>& wrong_ref = c_vec; //这是不行的
    const std::vector<int>& right_ref = c_vec; //都是const才能可以
    ```

- 输入输出流
    - std::istream：输入流类型，使用`>>`从流中获取数据。例如`std::cin`就是`std::istream`的一个实例，从控制台接受数据。每个`>>`以whitespace(包括tab, space, newline)为空格进行分割，将读到的部分放入变量。但是如果类型对不上，就会提前终止，而不会继续读取了。
        ```c++
        int x; 
        int y;
        std::cin>>x>>y;
        std::cout<<x<<y;
        ```
        如果上面的输入是`a,b`，那么输出是`0,0`。
    - std::ostream：输出流类型，使用`<<`将数据输出到流中。例如`std::cout`就是`std::ostream`的一个实例，将数据输出到控制台。
    - std::ifstream：输入文件流，从文件中读取数据到变量，这里使用`>>`只能读取到string类型，而不能像`std::cin >>`那样读取到其他类型(int，double等)。
        ```c++
        std::ifstream infile("in.text"); //自己初始化
        string str;
        infile >> str; //读取文件中的第一个字符。
        ```
    - std::ofstream：输出文件流，用法相似。

    - std::stringstream：字符串流，可以将字符串进行转换输入输出。例如将一个字符串转化为数字或者将带空格的一行字符串转化为多个数字。
        ```c++
        std::string input = "123";
        std::stringstream stream(input);
        int number;
        stream>>number; //"123"->123
        std::cout<<number<<std::endl;

        std::sting input1 = "1 2 3\n";
        std::stringstream stream1(input1);
        int a, b, c;
        stream1>>a; //1
        stream1>>b; //2
        stream1>>c; //3
        std::cout<<a<<" "<<b<<" "<<c<<std::endl;

        ```

- stl的container有迭代器，而container adaptor 没有迭代器，例如`stack`和`queue`，所以他们不能被遍历。迭代器可以看着一类专用于stl container的指针。

- 对于template class，我们不能直接像通常的class的实现方式进行实现，即在`.h`中声明，在`.cpp`中实现（`.cpp`中包含`.h`），然后再需要使用的文件中（如`main.cpp`）直接包含`.h`文件。这样的操作对于模板类在编译时会报错，参考[解释](https://isocpp.org/wiki/faq/templates#templates-defn-vs-decl)。解决方案一般推荐不拆分声明和定义，都在`.h`中实现，但这样会导致`.h`文件的膨胀。也可以拆分，参考[解决方案](https://www.codeproject.com/Articles/48575/How-to-Define-a-Template-Class-in-a-h-File-and-Imp)。

- 尽可能在能使用`const`的地方使用`const`来减少错误的发生。在类的定义中，不改变类成员的函数尽量使用`const`进行标记。区分const pointer 和 pointer to const：
    - const pointer: 指针本身是常量，不能改变指针指向，但可以改变指针指向的地址对于的值。
        ```c++
        int a = 1;
        int * const p = &a;
        *p = 2; //可以改变值
        // p = nullptr; //错误的，不能改变指针的指向
        ```
    - pointer to const: 指针指向一个常量，即不能改变指向地址的值，但可以改变指针的指向。

        ```c++
        int a = 1;
        const int * p = &a;
        //*p = 2; //不可以改变值
        p = nullptr; //可以改变指针的指向
        ```

- template functions使得函数可以通用，对类型进行参数化。这里也是在编译实例化时为特定的类型产生对应的代码。
    ```c++
    template <typename Type>
    Type mymin(Type a, Type b){
        return a<b? a:b;
    }

    //in main()
    cout<<myMin<int>(3,4)<<endl;
    ```

- [template metaprogramming](https://www.geeksforgeeks.org/template-metaprogramming-in-c/)：使用在编译时产生代码的机制，可以实现在编译时运行一次代码得到结果，然后在实际运行时就不会再运行该部分代码。
    ```c++
    template<unsigned n>
    struct Factorial{
        enum { value = n*Factorial<n-1>::value};
    };
    template<>
    struct Factorial<0>{
        enum { value = 1 };
    };

    //main()
    std::cout << Factorial<10>::value << endl;
    ```
    上面的代码在编译器编译到`Factorial<10>::value`，它会根据模板生成对应的代码，而由于生成的`value`是常量，所以又要生成`Factorial<9>::value`。然后一直到生成最后一个，计算得到`Factorial<10>::value`并保存下来。

- constexpr也可以使得C++程序在编译时运行。
    ```c++
    constexpr double fib(int n){
        if(n==1) return 1;
        return fib(n-1)*n;
    }

    int main(){
        const long long bigval = fib(10);//bigval会在编译时就生成，在运行时不用计算了。
        cout<<bigval<<endl;
    }

    ```

- 在编译时就计算结果可以提高性能，因为一次计算结果可能在运行时会使用多次。但这种方法在一般程序中使用较少。

- 善于使用function pointer, lambda来作为参数传递。短小的函数使用lambda，逻辑较为复杂的函数使用function pointer。
    ```c++
    int a = 8;
    auto add = [a](int b){ 
        return a+b;
    };

    int sum = add(6);    
    ```

- 父类中会被子类重载的函数需要使用`virtual`进行声明。

- 类里面可以对操作符进行重载，例如`+, -, <<, ==`等符号。可以作为member function 在类里进行重载，也可以作为non-member function在类外进行重载。在类外时，如果需要访问类的私有变量，需要在类里使用`friend`进行声明。
    ```c++
    class Student{
        public:
            //other functions

        bool operator < (const Student &rhs) const{ //member function
            return age<rhs.age; 
        }

        friend bool operator > (const Student &lhs, const Student &rhs) const; 

        private:
            int age;
            std::string name;

    }

    bool operator > (const Student &lhs, const Student &rhs){
        return lhs.age>rhs.age;
    }
    ```

- class中六个默认的特殊函数，只有调用时才会生成。
    ```c++
    class Widget{
        public:
            Widget(); //default constuctor, takes no parameters and creates a new object
            Widget(const Widget &w); //copy constructor, create a new object as a member-wise copy of another
            Widget &operator = (const Widget &w); //copy assignment operator, assign an already existing object to another
            ~Widget(); //destructor, called when an object goes out of scope
            Widget(Widget &&rhs); //move constructor
            Widget& operator = (Widget &&rhs); //move assigment operator
    }

    //example
    vector<int> func(vector<int> vec0){
        vector<int> vec1; //default constuctor
        vector<int> vec2(3); //parameter constructor
        vector<int> vec3{3}; //initializer list
        vector<int> vec4(); 
        vector<int> vec5(vec2); //copy constructor
        vector<int> vec6{};//default constuctor
        vector<int> vec7{vec3+vec4}; //copy constructor
        vector<int> vec8 = vec4; //copy constructor
        vec8 = vec2; //copy assignment operator
        return vec8; //copy constructor
    }
    ```
    - 使用initializer list 更高效
    - copy constructor是对每一个成员变量进行复制，但是有的时候成员变量是指针，那么两个类对象中的指针就指向了同一个地址，不是完全分离的深度拷贝。
    - 使用`delete`删除默认的函数，可以阻止对应的函数调用
        ```c++
        class Widget{
            public:
                Widget(); //default constuctor, takes no parameters and creates a new object
                Widget(const Widget &w) = delete; //copy constructor, create a new object as a member-wise copy of another
                Widget &operator = (const Widget &w) = delete; //copy assignment operator, assign an already existing object to another
                ~Widget(); //destructor, called when an object goes out of scope
                Widget(Widget &&rhs); //move constructor
                Widget& operator = (Widget &&rhs); //move assigment operator
        }
        ```
    - `move constructor`可以高效地处理临时变量，减少`copy constructor`的数据迁移。使用move后，原来的对象的指针会被指向空。
        ```c++
        #include <iostream>
        #include <string>

        using namespace std;

        class A
        {
        public:
            // default constructor
            A() : s("default") {}

            // copy constructor
            A(const A& o) : s(o.s) { cout << "move failed!\n";}

            // move constructor
            A(A&& o) : s(move(o.s)) {}

            string printObj() { return s; }
        private:
            string s;
        };

        A temp(A a) {
            return a;
        }

        int main()
        {
            // move-construct from rvalue temporary
            A a1 = temp(A());        

            // move-construct from lvalue 
            cout << "before move() : a1 = " << a1.printObj() << endl; 
            A a2 = move(a1);          
            cout << "after move() : a1 = " << a1.printObj() << endl;
            cout << "after move() : a2 = " << a2.printObj() << endl;

            return 0;
        }
        ```
        output
        ```
        before move() : a1 = default
        after move() : a1 = 
        after move() : a2 = default
        ```
        a1的指针已经被清空了。

    - `l-values`可以出现在`=`左边或者右边，有名字并且在局部范围内存在。`r-values`只能出现在`=`的右边，没有名字并且是零时的，只在一行有效，`r-values`是不能被引用(&)的。
        ```c++
        int x = 3; //3 is a r-value
        int vector<int> v1{1,2,3}; // {1,2,3} is an r-value
        auto v4 = v1+v2; //v1+v2 is a r-value
        ptr = &x; //&x is a r-value
        v1[2] = *ptr; //*ptr is a l-value, *ptr is x

        ```

    - 当`=`右边的是左值时，应该使用`copy assignment`；而当`=`右边是右值时，应该使用`move assignment`，因为右值本身马上就不需要了，可以直接把它的资源拿来使用。
        ```c++
        //using copy assignment
        vector<int> v1 = {1,2,4};
        vector<int> v2;
        v2 = v1;

        //using move assigment
        vector<int> v3 = make_a_vector(2); //this function return a vector
        ```
    - 我们在不同的场景下使用不同的assignment，这需要**编译器自动寻找合适的方法，即根据`=`右边的值是左值还是右值**。
        ```c++
        void change(int &&num){...}//version 1: can take a r-value
        void change(int &num){...}//version 2: take l-value

        int main(){
            int x = 1;
            change(x); //x is l-value, call version 2
            change(7); //7 is r-value call version 1
            return 0;
        }
        ```
        同理，适用于类的两个函数
        ```c++
        //copy assignment
        vector<T>& operator=(const vector<T>& other) 
        { 
            if (&other == this) return *this; 
            _size = other._size; 
            _capacity = other._capacity; 
            //must copy entire array
            delete[] _elems; 
            _elems = new T[other._capacity]; 
            std::copy(other._elems, other._elems + other._size, _elems); 
            return *this;
        }

        //move assignment
        vector<T>& operator=(vector<T>&& other)
        { 
            if (&other == this) return *this; 
            _size = other._size; 
            _capacity = other._capacity; 
            //we can steal the array
            delete[] _elems; 
            _elems = other._elems
            return *this; 
        }
        

        int main() {
            vector<int> vec;
            vec = make_me_a_vec(123); // this will use move assignment
            vector<string> vec1 = {"hello", "world"};
            vector<string> vec2;
            vec2 = vec1; // this will use copy assignment
            vec1.push_back("Sure hope vec2 doesn’t see this!");
        }
        ```
    - `std::move`只是将一个左值投射成右值，不改变其他的东西。至于投射后原来的对象是否还有值，取决于该类的move assignment方法的具体实现。

- `std::optional`用于对一个类型进行包装，在出错时进行判断。由于对性能有影响，所以一般不用。
    ```c++
    #include <iostream>
    #include <optional>
    std::optional<int> divide(int numerator, int denominator) {
        if (denominator != 0) {
            return numerator / denominator;
        } else {
            return std::nullopt;
        }
    }

    //如果不使用optional，要么在调用函数前确定除数不是0，要么就直接在divide中报错
    /*
    int divide(int numerator, int denominator){
        return numerator/denominator;
    }
    */

    int main() {
        std::optional<int> result = divide(10, 2); 

        if (result) { //可以根据返回值来判断而不是在divide里面判断然后直接终止程序。
            std::cout << "Result: " << result.value() << std::endl;
        } else {
            std::cout << "Division by zero occurred." << std::endl;
        }
        
        result = divide(10, 0);
        
        if (result) {
            std::cout << "Result: " << result.value() << std::endl;
        } else {
            std::cout << "Division by zero occurred." << std::endl;
        }

        return 0;
    }

    ```

- C++中使用智能指针来进行自动内存管理，避免内存泄漏。
    - std::unique_ptr：不能被复制，一个指针指向内存，当该指针过期时自动释放内存。使用std::make_unique()函数进行赋值，当然也可以使用new，但是基本不用。
        ```c++
        std::unique_ptr<T> up = std::make_unique<T>();
        ```
    - std::shared_ptr：可以被复制，多个指针指向一个内存（类似python的引用计数），当所有的指针都过期时，自动释放内存。std::make_shared()函数进行赋值，当然也可以使用new，但是基本不用。
        ```c++
        std::shared_ptr<T> sp1 = std::make_shared<T>();
        std::shared_ptr<T> sp2(sp1); //sp2和sp1指向同一个内存
        ```
    - std::weak_ptr：配合shared_ptr，可以查看有多少个shared pointer指向同一个内存，但是不能修改和使用数据。同时std::weak_ptr不会增加shared memory的引用计数。
        ```c++
        std::shared_ptr<T> sp1 = std::make_shared<T>();
        std::shared_ptr<T> sp2(sp1); 
        std::weak_ptr<T> wp = sp1;
        cout << wp.use_count() << endl; //2，sp1 和sp2两个指针
        sp2.reset(); //释放sp2
        cout << wp.use_count() << endl; //1，还剩sp1 
        ```
    智能指针的本质就是使用class的constructor和destructor来管理内存(RAII: Resource Acquisition is Initialization)。就是在constructor中分配资源，在destructor中释放资源以达到自动的资源管理，而不是像new和delete那样手动释放，容易造成内存泄漏。例如
    ```c++
    T *ptr = new T();
    //do something // 如果在do something中报错了，程序停止，就没有释放内存。
    delete ptr;
    ```
   这样的方式同样适合于文件的打开和关闭，资源上锁和解锁，都可以在class中实现来保证安全自动资源管理。