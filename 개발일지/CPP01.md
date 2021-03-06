# 개발 일지

## 2019. 09. 26.

### 마비노기 일일 쿠폰 계산기

** 되도록이면 하나의 반복문에서는 하나의 일만 하기!**

if (DOF == EDayOfWeek::일 || DOF == EDayOfWeek::토) 

반복문에서 날짜 초기화 까지 하려다가 영원히 일요일이 안오는 사태가 발생했음.

```cpp
#include <vector>
#include <iostream>

using namespace std;

enum class EDayOfWeek
{
	월,
	화,
	수,
	목,
	금,
	토,
	일
};


//마비노기 쿠폰 계산기!
// 
int Coupon(EDayOfWeek DOF, int days, int current_bonus)
{
	for (int i = 0; i < days; ++i)
	{
		current_bonus += 1;

		int id{ static_cast<int>(DOF) + 1 };
        
        //일주일은 7일 이다.
		if (id == 7) id = 0;

		DOF = static_cast<EDayOfWeek>(id);

		if (DOF == EDayOfWeek::일 || DOF == EDayOfWeek::토)
		{
			current_bonus += 1;
		}

	}

	return current_bonus;
}


int main()
{

	int z{};
	
	z = Coupon(EDayOfWeek::목, 14, 1);

	cout << z;

	return 0;
}
```



## 2019. 09. 27.

### cin으로 숫자 입력 받아 vector<>에 저장하기

cin으로 바로 vector에 입력받을 수 없으므로

`int input{};` 이라는 변수에 받은 후 `vector::emplace_back(input);` 한다.

```cpp
vector<int> v_cost{};

for (int i = 0; i < team_max; ++i)
{
    int input{};

    cin >> input;

    v_cost.emplace_back(input);
}
```



## 2019. 09. 28

### 알고리즘 문제 해결 방식,

- 한번에 하나씩 나누어서 생각하기
- **vector<int> v_sum{};**은 실제로 코드 상에서 count의 값이 변경되기 전에 초기화 해야함으로 count를 증가시키는 for문 안에 있어야한다.
- int 자료형 / int 자료형 의 계산은 int 형이 됨으로 float 값을 얻고 싶다면 강제 형변환 혹은 static_cast를 해준다.



```cpp

using namespace std;

//vector 안에 주어진 값을 offset과 count 에 따라서 더해줌
int get_sum(const vector<int>& v_cost, int offset, int count)
{
	int sum{};

	for (int i = 0; i < count; ++i)
	{
		sum += v_cost[i + offset];
	}

	return sum;
}


void find_min_average(const vector<int>& v_cost, int max, int min)
{
	

	vector<float> v_average{};

	//몇개씩 묶음
	for (int count = min; count < max + 1; ++count)
	{
		vector<int> v_sum{};

		//offset = 0부터 max - 1 count 가게 만들기
		for (int offset = 0; offset < max + 1 - count; ++offset)
		{
			v_sum.emplace_back(get_sum(v_cost, offset, count));

			// vector v_sum을 '오름차순'으로 정렬!
			sort(v_sum.begin(), v_sum.end());

			//가장 앞의 값이 가장 작은 수임으로 평균을 구해 평균의 vector average에 emplace_back 한다.
			//! 주의 int와 int값을 사용해서 나눗셈을 하기 때문에 float값을 얻기 위해서는 강제 형변환이 필요하다.
			v_average.emplace_back((float)v_sum[0] / count);
		}
	}

	//v_average는 각 count에서 get_sum을 할 시, 가장 적은 값이 들어와 있음으로 정렬을 하면 
	//각 항목에서 가장 작은 값이 v_average의 0번째 항목에 위치하게 된다.
	sort(v_average.begin(), v_average.end());
	
	cout << v_average[0];
}
```





### 	더 나은 코드 (''동적 계획법'''을 활용한 코드 작성)

-> 가장 많거나 작은 하나의 결과를 얻을 때 유용하다.

```cpp
using namespace std;

int get_sum(const vector<int> v_cost, int offset, int count)
{
	int sum{};

	for (int i = 0; i < count; ++i)
	{
		sum += v_cost[offset + i];
	}
	return sum;
}

void find_min_average(const vector<int>& v_cost, int team_max, int team_min)
{
	
	float min_average{ 3e+30f };

	for (int count = team_min; count < team_max + 1; ++count)
	{
		int min_sum{ 987'654'321 };
		for (int offset = 0; offset < team_max + 1 - count; ++offset)
		{
			min_sum = min(get_sum(v_cost, offset, count), min_sum);
		}
		min_average = min(min_average, (float)min_sum / count);
	}
	cout << min_average;
}
```





## 2019. 10. 01

### dfs 깊이 우선 탐색 방법



```cpp
#include <iostream>
#include <stack>
#include <vector>


//스텍을 활용한 깊이 우선 탐색(DFS) 구현하기 

/* 
	  /4
  / 2 \5
1 	 
  \ 3 /6 
	  \7
*/

using namespace std;

//노드의 갯수
int number{ 7 };

int checked[7]{};

//총 인덱스
vector<int> index[8]{};

void dfs(const int& x)
{
	//특정한 값이 그 노드를 방문했다면 함수를 끝낸다.
	if (checked[x]) return;

	//처음 방문한 것이라면 checked[x] 노드를 true로 방문 표시를 해준다.
	checked[x] = true;

	//그리고 그 노드를 출력
	cout << x << ' ';

	//인접 노드 하나씩 방문하기
	for (int i = 0; i < index[x].size(); ++i)
	{
		int y = index[x][i];

		//자기와 인접한 노드에 dfs를 실행하기
		dfs(y);
	}
}


int main()
{
	index[1].push_back(2);
	index[2].push_back(1);

	index[1].push_back(3);
	index[3].push_back(1);

	index[2].push_back(4);
	index[4].push_back(2);

	index[2].push_back(5);
	index[5].push_back(2);

	index[3].push_back(6);
	index[6].push_back(3);

	index[3].push_back(7);
	index[7].push_back(3);

	dfs(1);

	return 0;
}
```



## 2019 10. 05

### n개의 코드 중 m개를 고르는 모든 조합을 찾는 알고리즘

* 재귀 문제를 해결 해야할 때는 가장 먼저 기저 사례를 찾는다.

* 기저 사례는 자기 자신을 호출 할때 찾을 수 있다.

* 재귀 함수를 호출 할 때는 가장 깊은 곳 (ex) m =3 이면 0

  

```cpp
using namespace std;

void combination(const vector<int>& list, int n, int offset, vector<int>& p_list)
{
	if (n == 0) return;

    // 백터의 .size() 함수는 기본적으로 size_t 자료형이 기본이기 때문에 강제 형변환을 통해서 오류가 날 수 있는 부분을 사전 차단한다.
	for (int i = offset; i < (int)list.size(); ++i)
	{
		if (n > 1) p_list.emplace_back(list[i]);

        //재귀 함수를 할때는 자기 자신을 호출 할때 기저 사례를 찾을 수 있다.
		combination(list, n - 1, i + 1, p_list);
		//재귀 함수를 호출 할 때는 가장 깊은 곳에 있다고 가정한다.
        
		if (n == 1)
		{
			for (auto j : p_list)
			{
				cout << j;
			}

			cout << list[i] << endl;
		}
	}

	if (p_list.size()) p_list.pop_back();
}
```



###  재귀의 구조!

재귀의 구조를 파악하기!

* 재귀는 가장 '깊은 곳'까지 내려가서 실행이 된다.!
* ex) n = 0 까지 내려간다.

```cpp
void CallMyself(int n)
{
	if (n == 0) return;

	CallMyself(n - 1);

	cout << "나는 재귀(" << n << ")\n";
}
```

```cpp
나는 재귀(1)
나는 재귀(2)
나는 재귀(3)
나는 재귀(4)
나는 재귀(5)
```

```cpp
CallMyself(3);
{
	if (n == 0) return;

	CallMyself(2);
    {
        if (n == 0) return;

        CallMyself(1);
        {
            if (n == 0) return;

            CallMyself(0);
            {
                if (n == 0) return;

                // ... 실행 되지 않는다!
            }
            
            cout << "나는 재귀(" << 1 << ")\n";
        }
        
        cout << "나는 재귀(" << 2 << ")\n";
    }
    
	cout << "나는 재귀(" << 3 << ")\n";
}
```



### 범위 기반 for문 



```cpp
string s{ "hello" };
	for (char& c : s)
	{
  		// 여기서 C 는 'h', 'e', 'l', 'l', 'o'가 된다.
		cout << c;
	}
```



### 백터의 사용

​	백터의 크기가 0일 때를 항상 생각해야한다. (언더 플로우)

```cpp
vector<int> vec{};
int last{};

// 위험!!
vec.pop_back();
// vector의 size가 0이면 언더 플로우가 발생할 수 있다.

// 안전
if (vec.size()) vec.pop_back();


// 위험!!
last = vec.back();
// vector의 size가 0이면 back()은 vecotr의 마지막 값을 리턴해주는 함수 임으로 값을 불러 올 수 없다.

// 안전
last = (vec.size()) ? vec.back() : 0;
```



## 2019. 10. 14

### 피크닉 예재 

 예제 입출력기

```cpp
#include <iostream>
#include <vector>

using namespace std;

//stu == 학생의 수, pair == 짝의 수, v_friends == 

void findPair(int stu, int pair, vector<int> v_friends)
{

}

void findFriends(int stu, int pair, vector<int> v_friends)
{
	for (int i = 0; i < pair; ++i)
	{
		
	}
}



int main()
{
	//학생의 총 수 
	int stu{};
	cin >> stu;

	//학생들이 짝궁일 수
	int pair{};
	cin >> pair;

	//짝궁 친구들 
	vector<int> v_friends{};
	vector<int> v_pair{};

	// int 벡터에 저장하기 위한 int friends 변수
	int friends{};

	for (int i = 0; i < (int)(pair * 2); ++i)
	{
		cin >> friends;

		v_friends.emplace_back(friends);
	}

	return 0;
}
```



## 2019. 10. 18

### std::pair

pair를 사용하면 2개의 독립된 오브젝트를 하나의 배열 혹은 컨테이너에 묶을 수 있다.

```cpp
#include <utility>
#include <vector>
#include <string>
#include <iostream>

using namespace std;

int main()
{
	//pair를 받는 vector를 생성한다.
	vector<pair<string, int>> v_test_result{};

	v_test_result.push_back(pair<string, int>("하도윤", 60));
	v_test_result.push_back(pair<string, int>("김장원", 100));
	v_test_result.push_back(pair<string, int>("이승중", 65));

	//각 pair의 앞의 값은 first, 뒤의 값은 second로 접근 할 수 있다.
	//참조형 사용 - > 값을 넘겨준다.
	for (auto &i : v_test_result)
	{
		// 100점이 아닐시 동정 점수 + 5점
		if (i.second != 100)
		{
			i.second += 5;
		}
	}

	for (auto j : v_test_result)
	{
		
		cout << j.first << ' ' << j.second;
        
		if (j.second <= 70) cout << " 낙제!" << endl;
		else cout << " 합격!" << endl;
	}

	return 0;
}
```



### iterator 활용

int i로 시작하는 반복문과 반복자 iterator를 사용하는 반복을 헷갈리지 말자!

```cpp
#include <iostream>

int main()
{
    int numbers[] = { 1, 10, 100, 1000, 10001};
    
    for(auto& i : numbers)
    {   
        //i 가 곧 numbers의 각 항목이 된다.
        i += 1
    }
    
    return 0;
}
```





## 2019. 10. 20

### 피크닉 예제



```cpp
#include <iostream>
#include <vector>

using namespace std;

#define SAME_PAIR(a, b) (a.first == b.first || a.first == b.second || a.second == b.first || a.second == b.second)

// n == 아직 짝이 안정해진 학생의 수! 
void pairCount(int TotalStudentCount, int LeftStudentCount, 
	const vector<pair<int, int>>& v_pairs, vector<pair<int, int>>& v_past_pairs, int& p, int offset)
{
	//기저사례 n 이 0(더이상 학생이 남지 않았을 때
	if (LeftStudentCount == 0) return;

	for (int i = offset; i < (int)v_pairs.size(); ++i)
	{
		bool collision{ false };
		for (auto& past_pair : v_past_pairs)
		{
			if (SAME_PAIR(v_pairs[i], past_pair)) collision = true;
		}
		if (collision == false) v_past_pairs.emplace_back(v_pairs[i]);
		
		pairCount(TotalStudentCount, LeftStudentCount - 2, v_pairs, v_past_pairs, p, i + 1);

		if (v_past_pairs.size() == (TotalStudentCount / 2))
		{
			++p;
			v_past_pairs.pop_back();
		}
	}

	if (v_past_pairs.size()) v_past_pairs.pop_back();
}


int main()
{
	//학생의 수
	int n{};

	//친구 쌍의 수
	int pair_count{};

	//벡터에 친구 쌍 입력을 위한 int 변수 와 담아 둘 vector<int>
	int pair_input0{};
	int pair_input1{};
	vector<pair<int, int>> v_pairs{};

	int offset{};

	cin >> n;
	cin >> pair_count;

	for (int i = 0; i < pair_count; ++i)
	{
		cin >> pair_input0;
		cin >> pair_input1;
		
		if (pair_input0 > pair_input1)
		{
			swap(pair_input0, pair_input1);
		}
		
		//pair가 없을 때 만들어 준다.
		v_pairs.emplace_back(make_pair(pair_input0, pair_input1));
	}

	vector<pair<int, int>> v_p_pairs{};

	int p{};
	pairCount(n, n, v_pairs, v_p_pairs ,p, offset);

	cout << p;

	return 0;
}
```



## 2019. 10. 24

###  구체화와 일반화 

* 공부할 내용을 '무엇'을 공부할 지 생각하고(구체화) 다른 문제에 적용시키기 위해서 '왜 그렇게 해야하는가?'를 생각해야한다(일반화).

​	

```cpp
int main()
{
    //잘못된 변수 이름 짓기
    int number_of_students{};
    
    //더 나은 변수 이름 짓기
    //구체화 - 무엇을 잘못 생각했는가? 학생수를 number_of_students 로 생각함 더 나은 방법은 		  student_count이다.
    //일반화 - 이것이 왜 공부할 내용인가? _count가 ~의 수를 나타내는 변수 이름을 짓기에 더 나은 방법       이기 때문이다. -(복수를 사용하지 않아도 되며 students, 더 짧고, 전치사를 안쓴다.)
    int student_count{};
    
    return 0;
}
```



```cpp
// const를 쓸지 안 쓸지 잘 모르겠으면 일단 const를 쓴다.
void pairCombination(int student_count, int pair_count, const vector<pair<int, int>>& v_friend_pair)
{
    
}
```



```cpp
// v_matched_pair 안을 순회하면서 겹치는 것이 있는지 확인한다.
// 아래의 조건은 '안 겹치면'이라는 뜻이다. 이와같이 단순화 해서 생각해야한다.
if (matched_pair.first != v_friend_pair[i].first &&
    matched_pair.first !=v_friend_pair[i].second &&
    matched_pair.second != v_friend_pair[i].first &&
    matched_pair.second != v_friend_pair[i].second)
{

}
```

완전 탐색에서 재귀를 사용할 때 주의 점!

​	재귀를 사용할때는 깊이 별로 스택이 따로 생긴다. 

* 변수 값을 변경하기 위해서는 전역 변수나 매개변수를 사용한다.
* offset을 사용해야 중복이 발생하지 않는다.

```cpp
// | 깊이 0  | 깊이  1 | 깊이  2 |
// | [0, 1] | [2, 3]  | [4, 5] |

// ...

// | 깊이 0  | 깊이  1 | 깊이  2 |
// | [2, 3] | [0, 1]  | [4, 5] | // 각 깊이에서 for (int i = 0;;) 처럼 i가 0부터 시작하기 때문에 중복이 발생한다! 
```

### 디버깅 시 주의사항

* 디버깅을 실행할 때, F11로 한줄 한줄 실행하기 '전'에 어떻게 될 것인가를 생각하고 F11을 눌러 디버깅을 실행한다.





```cpp
struct SAny
{
    // x가 호출하는 생성자
    // 1. 생성자가 호출 되었을때 8번 줄로 이동 후, 7번에서 a가 초기화된다.
    // 2. 24번 줄로 이동해서 b를 2로 초기화한다.
    // 3. 9번 10번 줄로 이동해서 a에 4, b에 5가 대입된다.
	SAny() : a{ 3 }
	{
		a = 4;
		b = 5;
	}

    // y가 호출하는 생성자
    // 18번 줄로 이동하고 17번 줄로 이동해서 a와 b를 _a, _b로 초기화한다.
    // 19번 줄로 이동해서 a와 b에 8과 9를 대입한다.
    // 23, 24번째 줄의 a, b의 초기화가 무시된다.
	SAny(int _a, int _b) : a{ _a }, b{ _b }
	{
		a = 8;
		b = 9;
	}
	
	int a{ 1 };
	int b{ 2 };
};

int main()
{
    SAny x{};
    SAny y{ 6, 7 };
    
    return 0;
}
```





## 2019 .10. 29

### 인덱스와 접근

```cpp
vector<pair<int, int>> v_friend_pair{};

//베얄 선언시에는 [2]는 '2개의 항목'를 의미한다.
int input[2]{};


for (int i = 0; i < pair_count; ++i)
	{
    //접근시에 []안에 숫자는 접근할 배열의 주소를 의미한다.
		cin >> input[0];
		cin >> input[1];

		if (input[0] > input[1])
		{
			swap(input[0], input[1]);
		}

		v_friend_pair.emplace_back(make_pair(input[0], input[1]));
	}
```



### 디버깅시 주의 사항 2 

### if와 for

if문과 다르게 for문은 디버깅을 실행하면 for문으로 되돌아 가서 조건을 확인한다.

따라서 for문의 계산기 필요 없다고 판단되면 break로 필요없는 계산을 막아주는 것이 좋다.

```cpp
for (const auto& matched_pair : v_matched_pair)
{
    // v_matched_pair 와 하나라도 겹치면 v_matched_pair에 emplace_back을 하지 않는다.
    if (matched_pair.first == v_friend_pair[i].first ||
        matched_pair.first == v_friend_pair[i].second ||
        matched_pair.second == v_friend_pair[i].first ||
        matched_pair.second == v_friend_pair[i].second)
    {
        collision = true;
        
		//이 braek의 의미 = 하나라도 맞지 않다면 collision이 true가 되어 그 이후의 계산이 필요 없기 때문에 break를 해줘서 
        break;
    }
}
```





## 2019. 11. 1 게임판 덮기 문제 

### 2차원 벡터에 값 입력하기

```cpp
char input{};

vector<vector<char>> vv_buffer{};

// 
vv_buffer.resize(height);


for (int y = 0; y < height; ++y)
{
    for (int x = 0; x < width; ++x)
    {
        cin >> input;

        vv_buffer[y].emplace_back(input);	
    }
}
```



### 일관성있는 기준 잡기

```cpp
//l_block_type[블록 타입] [블록의 3개 위치] [블록 3개의 좌표값]
const int k_l_block_type[4][3][2]
{
	// ##
	//  #
	{ { 0, 0 }, { 0, 1 }, { 1, 1 } },
	// ##
	// #
	{ { 0, 0 }, { 1, 0 }, { 0, 1 } },
	// #
	// ##
	{ { 0, 0 }, { 1, 0 }, { 1, 1 } },
	//	#
	// ##
    { { 0, 1 }, { 1, 1 }, { 1, 0 } },
    
    
    /* -> 이런 식으로 기준점을 잘못 잡으면 문제 전체가 어려워 진다. => 일관성 유지하기
    
	{ { 0, 0 }, { 1, 0 }, { 1,-1 } },
	*/
    
};
```



## 2019. 11. 6

### offset의 관리

​	아래의 코드는 정상작동 하지 않는다.

​	offset_y가 0일 때는 for문을 빠져나올 때 coverBoard(vv_board, x + 1, y, type, result);로 인해 offset_x의 값이 증가함으로 offset_y가 1이 될때는 offset_x의 값이 coverBoard(vv_board, x + 1, y, type, result);의 이전 재귀 호출로 인해 (int)vv_board[0].size() - 2((int)vv_board[0].size() - 1까지 주회 함으로); 이기 되기 때문에 offset_y가 1인 시점 부터는 vv_board [y] [0]의 주소로 접근 할 수가 없다.

```cpp
void coverBoard(vector<vector<int>>& vv_board, int offset_x, int offset_y, int type, int& result)
{
	static int CommandIndex{};

	if (isFullBoard(vv_board))
	{
		++result;

		return;
	}


    //offset_y 
	for (int y = offset_y; y < (int)vv_board.size() - 1; ++y)
	{
        //잘못된 코드 - offset_x offset_y 가 1 인 시점부터
        // 재귀호출 때문에 offset_x는 0으로 접근 할 수 없다.
		for (int x = offset_x; x < (int)vv_board[0].size() - 1; ++x)
		{
			for (int type = 0; type < 4; ++type)
			{
				if (canCoverBoard(vv_board, x, y, type))
				{
					pushBlock(vv_board, x, y, type);

					coverBoard(vv_board, x + 1, y, type, result);

					popBlock(vv_board, x, y, type);
				}
			}
		}
		
	}
}
```



따라서,  offset_y가 1일 때, 부터 offset_x 가 0으로 점근하기 위해서는 offset_x의 for문에 다음과 같은 조건이 필요하다.

```cpp
//offset_y와 y의 값이 같을 때 x에 0을 대입한다. 아니라면 offset_x를 대입한다.
for (int x = (y == offset_y) ? offset_x : 0; x < (int)vv_board[0].size() - 1; ++x)
```



### 산술오버 플로우

int의 최대값 (+-2의 16승 ) 의 값을 넘을 소지가 있는 경우 컴파일러에서 그것을 방지하기 위해서 산술오버 플로우로 경고 해준다.

산술오버 플로우는 종종 예기치 못한 오류를 발생시킬 가능성이 존재함으로 더 큰 형변환 (long long)을 통해 해결해주는 것이 권장된다.

```cpp
void pushBlock(vector<vector<int>>& vv_board, int offset_x, int offset_y, int type)
{
	//타입이 0일 경우
	if (type == 0)
	{
		vv_board[(long long)offset_y + 0][(long long)offset_x + 0] = 1;
		vv_board[(long long)offset_y + 0][(long long)offset_x + 1] = 1;
		vv_board[(long long)offset_y + 1][(long long)offset_x + 1] = 1;
	}
	else if (type == 1)
	{
		vv_board[(long long)offset_y + 0][(long long)offset_x + 0] = 1;
		vv_board[(long long)offset_y + 0][(long long)offset_x + 1] = 1;
		vv_board[(long long)offset_y + 1][(long long)offset_x + 0] = 1;
	}
	else if (type == 2)
	{
		vv_board[(long long)offset_y + 0][(long long)offset_x + 0] = 1;
		vv_board[(long long)offset_y + 1][(long long)offset_x + 0] = 1;
		vv_board[(long long)offset_y + 1][(long long)offset_x + 1] = 1;
	}
	else if (type == 3)
	{
		vv_board[(long long)offset_y + 0][(long long)offset_x + 1] = 1;
		vv_board[(long long)offset_y + 1][(long long)offset_x + 0] = 1;
		vv_board[(long long)offset_y + 1][(long long)offset_x + 1] = 1;
	}
}
```



## 2019. 11. 10

### 벡터의 메소드 resize() 함수 활용

백터를 막 생성했을 때는 size가 0인 상태임으로 사이즈가 만약 미리 주어진다면 벡터의 사이즈를 resize로 미리 늘려놓는다.

```cpp
int main()
{
	int height{};
	int width{};

	cin >> height;
	cin >> width;

	vector<vector<int>> vv_board{};

    //vv_board의 사이즈가 주어진 경우, 오류를 막기위해 vv_board를 resize()한다.
	vv_board.resize(height);

	char input{};


	for (int y = 0; y < height; ++y)
	{
		for (int x = 0; x < width; ++x)
		{
			cin >> input;

			if (input == '#')
			{
				input = 1;
			}
			else if (input == '.')
			{
				input = 0;
			}

			vv_board[y].emplace_back(input);
		}
	}

	int result{};

	coverBoard(vv_board, 0, 0, 0, result);

	cout << result;

	return 0;
}
```



### 더욱 효율적인 알고리즘 만들기

1. 불필요한 계산 줄이기

해당 알고리즘은 '1번 항목'이 없다면 이미 해당 배열을 통과했을때 0 이 남아 기저사례에 도달하지 않는다고 할지라도 계속해서 L자형 블록을 넣어 확인한다.



```cpp
void coverBoard(vector<vector<int>>& vv_board, int offset_x, int offset_y, int type, int& result)
{
	if (isFullBoard(vv_board))
	{
		++result;

		return;
	}


	for (int y = offset_y; y < (int)vv_board.size() - 1; ++y)
	{
		for (int x = (y == offset_y) ? offset_x : 0; x < (int)vv_board[0].size() - 1; ++x)
		{
            // 1번 항목 x가 1 이상일 때, 
			if (x >= 1)
			{
				for (int _x = 0; _x < x; ++_x)
				{
					if (vv_board[y][_x] == 0)
					{
						return;
					}
				}
			}

			for (int type = 0; type < 4; ++type)
			{
				if (canCoverBoard(vv_board, x, y, type))
				{
					pushBlock(vv_board, x, y, type);

					coverBoard(vv_board, x + 1, y, type, result);

					popBlock(vv_board, x, y, type);
				}
			}
		}
        
        //pushBlock으로 인해 블록이 들어갔음에도 
		for (int x = 0; x < (int)vv_board[0].size(); ++x)
		{
			if (vv_board[y][x] == 0)
			{
				return;
			}
		}
	}
}
```



자주하는 실수

```cpp
bool canCoverBoard(vector<vector<int>>& vv_board, int offset_x, int offset_y, int type)
{
	bool can_cover{ false };

	// type 0
	if (type == 0 &&
		vv_board[(long long)offset_y + 0][(long long)offset_x + 0] == 0 &&
		vv_board[(long long)offset_y + 0][(long long)offset_x + 1] == 0 &&
		vv_board[(long long)offset_y + 1][(long long)offset_x + 1] == 0)
	{
		can_cover = true;
	} 
    // type 1
	else if (type == 1 &&
		vv_board[(long long)offset_y + 0][(long long)offset_x + 0] == 0 &&
		vv_board[(long long)offset_y + 0][(long long)offset_x + 1] == 0 &&
		vv_board[(long long)offset_y + 1][(long long)offset_x + 0] == 0)
	{
		can_cover = true;
	} 
    // type 2
	else if (type == 2 &&
		vv_board[(long long)offset_y + 0][(long long)offset_x + 0] == 0 &&
		vv_board[(long long)offset_y + 1][(long long)offset_x + 0] == 0 &&
		vv_board[(long long)offset_y + 1][(long long)offset_x + 1] == 0)
	{
		can_cover = true;
	} 
    // type 3
	else if (type == 3 &&
		vv_board[(long long)offset_y + 0][(long long)offset_x + 1] == 0 &&
		vv_board[(long long)offset_y + 1][(long long)offset_x + 0] == 0 &&
		vv_board[(long long)offset_y + 1][(long long)offset_x + 1] == 0)
	{
		can_cover = true;
	}

	return can_cover;
}
```


## 함수에 시그니쳐가 포함되어있지 않습니다.
```cpp
	float getWorth();
	//함수의 시그니처가 포함되어있지 않은 형태
	float getWorth{};

```