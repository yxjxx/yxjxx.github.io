---
layout: post
title: 求集合全子集
date: 2021-03-07 23:27
tags: [Algorithm]
---

题目：集合求全子集

```objc
例如:
{1，2，3} -> { {}, {1}, {2}, {3}, {1,2}, {1,3}, {2,3}, {1,2,3} }
```

参考：http://www.mamicode.com/info-detail-1269237.html

## 解法一：二进制法

```objc
- (void)viewDidLoad {
    [super viewDidLoad];
   
    [self p_allSubSet];
}

- (void)p_allSubSet {    
    NSArray *arr = @[@"a", @"b", @"c"];
    NSSet *input = [NSSet setWithArray:arr];
    NSSet *result = [self wholeSubSet:input];
    NSLog(@"input set: %@, all subset result: %@", input, result);
}

//集合的全子集 数量肯定是 2的n次方（n为集合中元素的数量）
//转换成一个二进制亮灯模型，一个灯可以点亮或者熄灭（对应是否选到集合中）
- (NSMutableSet *)wholeSubSet:(NSSet *)input {
    NSInteger n = [input count];
    NSMutableSet *result = [NSMutableSet set];

    for (int i = 0;i <= pow(2, n) - 1;i++) {
        NSMutableSet *inputM = [input mutableCopy];
        __block NSInteger index = 0;
        [input enumerateObjectsUsingBlock:^(id  _Nonnull obj, BOOL * _Nonnull stop) {
            if ([self lightUp:i atIndex:index]) {
                //do nothing
            } else {
                [inputM removeObject:obj];
            }
            index++;
        }];
        [result addObject:inputM];
    }
    return result;
    /*
     时间复杂度：O(n*2^n)。
     空间复杂度为O(n)。
     */
}

///> 判断对应位置的灯泡是否可以点亮
- (BOOL)lightUp:(NSInteger)i atIndex:(NSInteger)index {
    //i = 1(001)
    NSInteger bit = pow(2,index);//（index 0, 001; 1, 010; 2 100)
    if (i & bit) {//与 都为1 才为1
        return YES;
    } else {
        return NO;
    }
}
```


## 解法二：递归法

```objc
- (NSMutableSet *)wholeSubSet2:(NSSet *)input {
    /*
     设。原始集合s=<a,b,c,d>，子集结果为r：
     第一次迭代： r=<a> 第二次迭代：r=<a ab b>
     第三次迭代： r=<a ab b ac abc bc c>
     第四次迭代：r=<a ab b ac abc bc c ad abd bd acd abcd bcd cd d>
     */
    NSMutableSet *result = [NSMutableSet set];
    NSInteger index = 0;
    for (NSString *str in input) {
        if (index == 0) {
            NSMutableSet *aSet = [NSMutableSet set];
            [aSet addObject:str];
            [result addObject:aSet];
        } else {
            //上次的结果result + 上次的结果join这次的元素（joinSet:obj:） + 这次的元素str
            NSSet *combinedSet = [self joinSet:result obj:str];
            for (NSSet *cSet in combinedSet) {
                [result addObject:cSet];
            }
            NSMutableSet *aSet = [NSMutableSet set];
            [aSet addObject:str];
            [result addObject:aSet];//这次的元素
        }
        index++;
    }
    NSSet *emptySet = [NSSet set];
    [result addObject:emptySet];
    return result;
}

- (NSMutableSet<NSSet *> *)joinSet:(NSSet <NSSet *>*)set obj:(NSString *)str {
    NSMutableSet *result = [NSMutableSet set];
    for (NSSet *theSet in set) {
        NSMutableSet *setM = [NSMutableSet setWithSet:theSet];
        [setM addObject:str];
        [result addObject:setM];
    }
    return result;
}
```