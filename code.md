reads number from standard input
``` cpp
#include <iostream>

int main() {
	std::cout << "Please enter two numbers: ";

	auto a = 0;
	auto b = 0;
	if (not (std::cin >> a >> b)) {
		std::cerr << "Something went wrong while reading an integer, bailing...\n";
		return 1;
	}

	std::cout << "Sum: " << (a + b) << '\n';
	return 0;
}
```

sort in descending order
``` cpp
auto sort_descending(std::vector<std::string>& numbers) -> void {
	std::sort(numbers, std::greater{});
}
```

enumeration class
``` cpp
enum class paint : unsigned char {
    RED,
    YELLOW,
    BLUE,

    ORANGE,
    PURPLE,
    GREEN
};
```

usage of optional
``` cpp
auto subtractive_color(paint p1, paint p2) -> std::optional<paint> {
    auto new_colour = std::optional<paint>{};
    if (p1 == paint::RED && p2 == paint::YELLOW) {
        new_colour = paint::GREEN;
    } else if (p1 == paint::RED && p2 == paint::BLUE) {
        new_colour = paint::PURPLE;
    } else if (p1 == paint::YELLOW && p2 == paint::BLUE) {
        new_colour == paint::GREEN;
    } else {
        // invalid colour combination
    }

    return new_colour;
}
```

lambda function
``` cpp
TEST_CASE("Checks sort3(int&, int&, int&)") {
  std::vector<std::string> vec{"We", "love", "lambda", "functions"};

  auto const vowels = [] (char const& c) { return c == 'a' || c == 'e' || c == 'i' || c == 'o' || c == 'u'; };

  std::sort(vec.begin(), vec.end(), [vowels] (std::string const& x, std::string const& y) {
    auto const xcount = std::count_if(x.cbegin(), x.cend(), vowels);
    auto const ycount = std::count_if(y.cbegin(), y.cend(), vowels);
    if (xcount == ycount) {
      return (x.length() - static_cast<std::string::size_type>(xcount)) > (y.length() - static_cast<std::string::size_type>(ycount));
    }
    return xcount > ycount;
  });

  std::vector<std::string> correct_order{"functions", "lambda", "love", "We"};
  CHECK(correct_order == vec);
}
```

Matrix class
``` cpp
class Matrix{
public:


    Matrix():Matrix{1,1}{}
    Matrix(int row, int col):Matrix{row,col,0}{}
    Matrix(int row, int col, int val):row_{row},col_{col},vals_{std::make_unique<std::unique_ptr<int[]>[]>(row_)}{
        for(auto i =0;i<row_;++i){
            vals_[i] = std::make_unique<int[]>(col_);
            std::fill(vals_[i].get(),vals_[i].get()+col_,val);
        }

    }


    Matrix(Matrix const& cp):Matrix{cp.row_,cp.col_}{

        for(auto i =0;i<row_;++i){
            std::copy(cp.vals_[i].get(),cp.vals_[i].get()+col_,vals_[i].get());
        }
    }

    Matrix(Matrix&& mv):row_{std::exchange(mv.row_,0)},col_{std::exchange(mv.col_,0)},vals_(std::move(mv.vals_)){}
    auto operator=(Matrix&& mv)->Matrix&{
        row_=std::exchange(mv.row_,0);
        col_=std::exchange(mv.col_,0);
        vals_=std::move(mv.vals_);
        return *this;
    }


    auto operator=(Matrix const& cp)->Matrix&{
        auto copy = cp;
        std::swap(copy, *this);
        return *this;
    }
    auto get(int x,int y) ->int&{
        if(!(x>=0 and x<row_ and y >=0 and y < col_))  throw std::runtime_error("out of bound");
        return vals_[x][y];
    }

    auto get(int x,int y) const ->int{
        if(!(x>=0 and x<row_ and y >=0 and y < col_))  throw std::runtime_error("out of bound");
        return vals_[x][y];
    }
    friend auto operator+(const Matrix& lhs, const Matrix& rhs)->Matrix{
        if(lhs.col_ == rhs.col_ and lhs.row_ == rhs.row_){
            auto new_matrix = Matrix(lhs.row_,lhs.col_);
            for(auto i =0;i<lhs.row_;++i){
                for(auto j =0;j<lhs.col_;++j){
                    new_matrix.vals_[i][j] = lhs.vals_[i][j]+rhs.vals_[i][j];
                }

            }
            return new_matrix;

        }
        throw std::runtime_error("size mismatch");
    }

    auto operator+=(const Matrix& rhs)->Matrix{
        if(col_ == rhs.col_ and row_ == rhs.row_){

            for(auto i =0;i<row_;++i){
                std::transform(vals_[i].get(), vals_[i].get()+col_, rhs.vals_[i].get(),
                               vals_[i].get()+col_, std::plus<>{});

            }
            return *this;

        }
        throw std::runtime_error("size mismatch");
    }

    ~Matrix()=default;
private:
    int row_,col_;
    std::unique_ptr<std::unique_ptr<int[]>[]> vals_;


};

int main() {
    auto m = Matrix(2,2,1);
    auto mm = std::move(m);

    std::cout << mm.get(0,0) <<std::endl;


    return 0;
}
```

concept example
``` cpp
template <typename T>
concept Animal = std::default_initializable<T>
        && std::copy_constructible<T>
        && std::move_constructible<T>
        && requires (T t) {
    { t.cry() } -> std::same_as<std::string>;
    { t.which() } -> std::same_as<std::string>;
};
```

derived exception
``` cpp
struct ExceptionBase
{
    virtual void raise() { throw *this; }
    virtual ~ExceptionBase() {}
};
struct ExceptionDerived : ExceptionBase
{
    virtual void raise() { throw *this; }
};

void func(ExceptionBase& e)
{
    e.raise();
}
TEST_CASE("exception handling")
{
    ExceptionDerived e;
    CHECK_THROWS_AS( func(e), ExceptionDerived);
}
```

copy_if
``` cpp
std::copy_if(
      std::istream_iterator<std::string>{input},
      std::istream_iterator<std::string>{},
      std::ostream_iterator<std::string>{output, " "},
      [&](const std::string& s) {
        return std::find(valid_words.begin(), valid_words.end(), s) != valid_words.end();
      });
```

namespace traits
``` cpp
namespace traits {

template <typename T>
struct is_a_pointer {
	static auto constexpr value = false;
};

template <typename T>
struct is_a_pointer<T*> {
	static auto constexpr value = true;
};

} // namespace traits
```
