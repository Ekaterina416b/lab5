#lab05  
```
lab05 % mkdir third-party
% git submodule add https://github.com/google/googletest third-party/gtest
>>Cloning into '/Users/makbuk/lab05/third-party/gtest'...
remote: Enumerating objects: 28040, done.
remote: Counting objects: 100% (267/267), done.
remote: Compressing objects: 100% (173/173), done.
remote: Total 28040 (delta 175), reused 94 (delta 94), pack-reused 27773 (from 3)
Receiving objects: 100% (28040/28040), 13.55 MiB | 1.00 MiB/s, done.
Resolving deltas: 100% (20772/20772), done.
% touch CMakeLists.txt && nano CMakeLists.txt
```
Содержимое CMakeLists.txt:

<img width="378" alt="Снимок экрана 2025-05-08 в 15 29 40" src="https://github.com/user-attachments/assets/7e59346e-14a5-4ba5-a98d-33c76d0fd6a1" />  


```  
% mkdir -p tests && touch tests/tests.cpp
% open -a Xcode tests/tests.cpp  
```
Содержимое tests.cpp:

```
#include <gtest/gtest.h>
#include <gmock/gmock.h>

#include "Account.h"
#include "Transaction.h"

using ::testing::Return;
using ::testing::Throw;
using ::testing::_;

class MyAccount : public Account {
public:
    MyAccount(int id, int balance) : Account(id, balance) {}
    MOCK_CONST_METHOD0(GetBalance, int());
    MOCK_METHOD1(ChangeBalance, void(int diff));
    MOCK_METHOD0(Lock, void());
    MOCK_METHOD0(Unlock, void());
};

TEST(Account, Locker) {
    MyAccount acc(0, 1111);
    EXPECT_CALL(acc, Lock()).Times(2);
    EXPECT_CALL(acc, Unlock()).Times(1);
    acc.Lock();
    acc.Lock();
    acc.Unlock();
}

TEST(Account, balance_positive) {
    Account acc1(0, 1000);
    EXPECT_EQ(acc1.GetBalance(), 1000);

    acc1.Lock();
    EXPECT_NO_THROW(acc1.ChangeBalance(100));

    EXPECT_EQ(acc1.GetBalance(), 1100);
}

TEST(Accout, balance_negative) {
    Account Vasya(1, 100);

    EXPECT_THROW(Vasya.ChangeBalance(100), std::runtime_error);
    
    Vasya.Lock();
    EXPECT_ANY_THROW(Vasya.Lock());
}

TEST(Transaction, construnct_and_positive) {
    Transaction first;
    EXPECT_EQ(first.fee(), 1);

    Account Petya(0, 6132);
    Account Katya(1, 2133);

    first.set_fee(32);
    EXPECT_EQ(first.fee(), 32);

    EXPECT_TRUE(first.Make(Petya, Katya, 100));
    EXPECT_EQ(Katya.GetBalance(), 2233);
    EXPECT_EQ(Petya.GetBalance(), 6000);

}

TEST(Transaction, negative) {
    Transaction second;
    second.set_fee(51);
    Account Roma(0, 10);
    Account Misha(1, 1000);

    EXPECT_THROW(second.Make(Misha, Misha, 0), std::logic_error);

    EXPECT_THROW(second.Make(Misha, Roma, -100), std::invalid_argument);

    EXPECT_THROW(second.Make(Misha, Roma, 50), std::logic_error);

    EXPECT_FALSE(second.Make(Misha, Roma, 100));

    second.set_fee(10);

    EXPECT_FALSE(second.Make(Roma, Misha, 100));
    
}
```  
```
% mkdir -p coverage && touch coverage/lcov.info
```
```
mkdir -p .github/workflows
touch .github/workflows/lab.yml
```

