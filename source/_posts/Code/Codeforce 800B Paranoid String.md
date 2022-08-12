---
title: Codeforce R800 Div2
date: 2022-06-17 00:55:57
tags:
---
## Problem Statement [ParanoidString](https://codeforces.com/problemset/problem/1694/B)

"01" -> "

## Solution

## Code

```java
import java.util.Scanner;

/**
 * @author macinchang
*  Created on 2022-06-16 22:30:30
 */
public class ParanoidString {

    public static void main(String[] args) throws Exception {
        Scanner scanner = new Scanner(System.in);
        int t = scanner.nextInt();
        while (t-- > 0) {
            new Solver().solve(scanner);
        }
        scanner.close();
    }

    public static class Solver {
        public void solve(Scanner scanner) {
            long n = scanner.nextLong();
            String str = scanner.next();
            char[] s = str.toCharArray();
            long ans = n * (n + 1) / 2;
            for (int i = 1; i < n; i++) {
                if (s[i] == '0' && s[i - 1] == '0') {
                    ans -= i;
                }
                if (s[i] == '1' && s[i - 1] == '1') {
                    ans -= i;
                }
            }
            System.out.println(ans);
        }
    }
}

```

