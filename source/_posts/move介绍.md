---
layout: post
title: move介绍
date: 2022-10-22 13:09:00
tags: blockchain move aptos
---

# move背景

move 是由 [Diem 项目](https://diem.com/) 专门为区块链开发的一种安全可靠的智能合约编程语言 ,目前主要使用move语言开发智能合约的链有 [Sui](https://sui.io/)  ，[Aptos](https://aptoslabs.com/) ，[Starcoin](https://starcoin.org)

<img title="" src="https://raw.githubusercontent.com/aranoverse/pic-host/main/blog/move_diff_from_solidity/move_background.png" alt="">

# move对比solidity

## 存储模型

![](https://raw.githubusercontent.com/aranoverse/pic-host/main/blog/move_diff_from_solidity/move_state.png)

```move
struct GlobalStorage {
  resources: Map<(address, ResourceType), ResourceValue>
  modules: Map<(address, ModuleName), ModuleBytecode>
}

// Operators 
move_to<T>(&signer,T)    
move_from<T>(address): T    
borrow_global_mut<T>(address): &mut T    
borrow_global<T>(address): &T 
exists<T>(address): bool  


module 0x1::coin::Coin<0x666::alpha_module::aranoverse>

struct Coin<phantom CoinType> has store {
        /// Amount of coin this address has.
        value: u64,
    }
```

![](https://raw.githubusercontent.com/aranoverse/pic-host/main/blog/move_diff_from_solidity/solidity_state.png)

```solidity
contract Alpha{
    mapping(address=>uint256) balance;
}

contract Beta{
    mapping(uint256=>address) ownerships;
}
```

## 面向资源

move 使用drop store key copy这样一些关键字赋予资源各种能力（ability），实现了数据的**资源属性**，使其不再是单纯的一个uint,一个struct

key代表可以作为全局存储中的索引，一般和address组合查询

drop代表该数据可以在未转移的情况下丢弃

store 代表存储结构

copy 赋予资源快速拷贝的能力，比如权限之类的

```move
// @see module aptos_framework::coin
struct CoinStore<phantom CoinType> has key {
        coin: Coin<CoinType>,
        frozen: bool,
        deposit_events: EventHandle<DepositEvent>,
        withdraw_events: EventHandle<WithdrawEvent>,
  }


module hello_aptos::resource {
    #[test_only]
    use aptos_framework::account;
    #[test_only]
    use aptos_framework::coin;
    #[test_only]
    use aptos_framework::managed_coin;

    struct Aranoverse {}


    #[test(manager = @hello_aptos)]
    fun test_mint(manager: address){
        let signer = account::create_account_for_test(manager);
        managed_coin::initialize<Aranoverse>(&signer, b"Aranoverse", b"AVT", 18, true);

        coin::register<Aranoverse>(&signer);
        assert!(coin::is_coin_initialized<Aranoverse>(), 1);

        managed_coin::mint<Aranoverse>(&signer, manager, 100000000000);
        assert!(coin::balance<Aranoverse>(manager) == 100000000000, 2);

        // Could not compile , Cannot ignore values without the 'drop' ability. The value must be used
        // let _coin_resource = coin::withdraw<Aranoverse>(&signer, 100);
    }
}
```

ERC20部分代码

```solidity
    function _transfer(
        address from,
        address to,
        uint256 amount
    ) internal virtual {
        require(from != address(0), "ERC20: transfer from the zero address");
        require(to != address(0), "ERC20: transfer to the zero address");

        _beforeTokenTransfer(from, to, amount);

        uint256 fromBalance = _balances[from];
        require(fromBalance >= amount, "ERC20: transfer amount exceeds balance");
        unchecked {
            _balances[from] = fromBalance - amount;
        }
         // 如果这里的amount不进行加减
        _balances[to] += amount;

        emit Transfer(from, to, amount);

        _afterTokenTransfer(from, to, amount);
    }
```

一句话来讲move 提供的 ability ，限定了**资源必须被处理** 这样的一个语境

## 泛型

1. 逻辑复用

2. 安全复用

3. 真正的协议标准复用，但是丢失了一定的灵活性

```move
struct Aranoverse {}

managed_coin::initialize<Aranoverse>(&signer, b"Aranoverse", b"AVT", 18, true);
```

```solidity
contract Aranoverse is ERC20{
    constructor()ERC20("Aranoverse","AVT"){}

  // Todo: self-defined logic
}
```

## 形式化验证

简单来说可以认为是运行前断言以及运行后断言，可以保证运行的正确性。

```move
module hello_aptos::counter {
    struct Counter has key {
        value: u8,
    }

    public fun increment(addr: address) acquires Counter {
        let r = borrow_global_mut<Counter>(addr);
        r.value = r.value + 1;
    }


    spec increment {
        // increment运行前
        let conter = global<Counter>(addr).value;


        // increment运行后
        let post conter_post = global<Counter>(addr).value;
        // 断言数据
        ensures conter_post == conter + 1;
    }

}
```

## 自带测试框架

```move
  #[test(signer = @hello_aptos)]
    public fun test_increment(signer: &signer) acquires Counter {
        increment(signer);
        let counter_log_1 = counter(address_of(signer));
        increment(signer);
        let counter_log_2 = counter(address_of(signer));
        assert!(counter_log_1 + 1 == counter_log_2, 1);
    }
```

# move on Aptos 简要开发示例

## 环境准备

1. rust 环境    
   
   ```shell
   curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
   ```

2. aptos cli
   
   ```shell
   cargo install --git https://github.com/aptos-labs/aptos-core.git aptos  --tag  aptos-cli-v1.0.0
   ```

## move 开发

1. ```shell
   # 创建项目
   mkdir hello-aptos && cd hello-aptos && aptos move init --name hello-aptos
   ```

2. ```shell
   # 初始化 rpc 以及 account
   aptos init
   ```

3. ```move
   // sources/hello_aptos.move
   
   module hello_aptos::message3 {
       use std::error;
       use std::signer;
       use std::string;
   
       use aptos_framework::account;
       use aptos_framework::event;
   
       struct MessageHolder has key {
           message: string::String,
           message_change_events: event::EventHandle<MessageChangeEvent>,
       }
       //<:!:resource
   
       struct MessageChangeEvent has drop, store {
           from_message: string::String,
           to_message: string::String,
       }
   
       const ENO_MESSAGE: u64 = 0;
   
       public fun get_message(addr: address): string::String acquires MessageHolder {
           assert!(exists<MessageHolder>(addr), error::not_found(ENO_MESSAGE));
           *&borrow_global<MessageHolder>(addr).message
       }
   
       public entry fun set_message(account: signer, message: string::String)
       acquires MessageHolder {
           let account_addr = signer::address_of(&account);
           if (!exists<MessageHolder>(account_addr)) {
               move_to(&account, MessageHolder {
                   message,
                   message_change_events: account::new_event_handle<MessageChangeEvent>(&account),
               })
           } else {
               let old_message_holder = borrow_global_mut<MessageHolder>(account_addr);
               let from_message = *&old_message_holder.message;
               event::emit_event(&mut old_message_holder.message_change_events, MessageChangeEvent {
                   from_message,
                   to_message: copy message,
               });
               old_message_holder.message = message;
           }
       }
   ```
   
   }          

4. ```move
   // tests/hello_aptos_test.move
    #[test_only]
   module hello_aptos::message_tests {
       use std::signer;
       use std::unit_test;
       use std::vector;
       use std::string;
   
       use hello_aptos::message3;
   
       fun get_account(): signer {
           vector::pop_back(&mut unit_test::create_signers_for_testing(1))
       }
   
       #[test]
       public entry fun sender_can_set_message() {
           let account = get_account();
           let addr = signer::address_of(&account);
           aptos_framework::account::create_account_for_test(addr);
           message3::set_message(account,  string::utf8(b"Hello, Aptos"));
   
           assert!(
               message3::get_message(addr) == string::utf8(b"Hello, Aptos"),
               0
           );
       }
   }
   ```

5. ```toml
   [package]
   name = 'hello-aptos'
   version = '1.0.0'
   
   [dependencies.AptosFramework]
   git = 'https://github.com/aptos-labs/aptos-core.git'
   rev = 'main'
   subdir = 'aptos-move/framework/aptos-framework'
   
   [addresses]
   hello_aptos = "0x000b2e79cbfbf4bb872769c53fae1827f460016893aafb678c90fa2437ae6c96"
   aptos_framwork= "0x1"
   ```

6. ```shell
   aptos move test
   ```

7. ```shell
   aptos move publish --named-addresses hello_aptos=0x000b2e79cbfbf4bb872769c53fae1827f460016893aafb678c90fa2437ae6c96
   ```

8. ```shell
   aptos move run \
    --function-id 'default::message3::set_message' \
    --args 'string:hello, atptos'
   ```

9. ```shell
   curl --location --request GET 'https://fullnode.devnet.aptoslabs.com/v1/accounts/{YOUR_ACCOUNT}/resource/{YOUR_ACCOUNT}::message3::MessageHolder'
   ```

# 文献与代码

[The Move Book](https://move-language.github.io/move/)

[move/spec-lang.md at main · move-language/move · GitHub](https://github.com/move-language/move/blob/main/language/move-prover/doc/user/spec-lang.md)

[GitHub - aptos-labs/aptos-core: A layer 1 for everyone!](https://github.com/aptos-labs/aptos-core)

[GitHub - aranoverse/hello-aptos: Hello world on  Aptos](https://github.com/aranoverse/hello-aptos)

[move介绍和简明开发案例 - YouTube](https://www.youtube.com/watch?v=EQWC3wq3wtY)
