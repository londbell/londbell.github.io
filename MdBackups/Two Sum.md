 1. Two Sum 

Given an array of integers, return indices of the two numbers such that they add up to a specific target.

You may assume that each input would have exactly one solution, and you may not use the same element twice.

Example:

Given nums = [2, 7, 11, 15], target = 9,

Because nums[0] + nums[1] = 2 + 7 = 9,
return [0, 1].

package com.company;

public class Main {

    public static void main(String[] args) {
    // write your code here
        int nums3[] = {3, 2, 4};
        int target3 = 6;
        int nums4[] = {2,7,11,15};
        int target4 = 9;
        int twoSum[] = Solution.twoSum(nums3, target3);
        System.out.print(twoSum[0] + " " + twoSum[1]);
        System.out.println(" finish");
    }
}

class Solution {
    public static int[] twoSum(int[] nums, int target) {
        int flag = 0;
        int array[] = {0,0};
        for(int i = 0; i < nums.length; i++) {
            if(flag == 1) {
                break;
            }

            for(int j = i + 1; j < nums.length; j++) {
                if((nums[i] + nums[j] == target)) {
                    array[0] = i;
                    array[1] = j;
                    flag = 1;
                    break;
                }
            }
        }
        return array;
    }
}
