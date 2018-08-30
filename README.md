// Attempt to guess the big O complexity of a function

package com.company;

import java.util.function.Function;

public class Main {

  public static void main(String[] args) {
    // write your code here

    Function<Long, Long> constantComplexity = makeFn(input -> 25L);

    Function<Long, Long> logComplexity = makeFn((input -> (long) Math.log(input.doubleValue())));

    Function<Long, Long> linear = makeFn(input -> input * 3);

    Function<Long, Long> poly = makeFn(input -> (long) Math.pow(input, 3));

    Function<Long, Long> exp = makeFn(input -> (long) Math.pow(input, input));

    System.out.println("Expected constant time - " + bigO(constantComplexity));
    System.out.println("Expected log time - " + bigO(logComplexity));
    System.out.println("Expected linear time - " + bigO(linear));
    System.out.println("Expected polynomial time - " + bigO(poly));
    System.out.println("Expected exponential time - " + bigO(exp));
  }

  static Function<Long, Long> makeFn(Function<Long, Long> complexity) {

    return n -> {
      Long x = Long.valueOf(0);
      for (long i = 0; i < complexity.apply(n); i++) {
        // do something here...
        x++;
      }

      return x;
    };
  }

  // Write a class that will identify the Big-O runtime of this function.
  static String bigO(Function<Long, Long> fn) {
    Long input1 = 1L;
    Long input2 = 2L;
    Long input5 = 5L;

    Long output1 = fn.apply(input1);
    Long output2 = fn.apply(input2);
    Long output5 = fn.apply(input5);
    double logOutput2 = Math.log(output2);
    double logOutput1 = Math.log(output1);
    double logOutput5 = Math.log(output5.doubleValue());
    double logInput1 = Math.log(input1);
    double logInput2 = Math.log(input2);
    double logInput5 = Math.log(input5.doubleValue());
    double slopeLogLog1 = (logOutput5 - logOutput2) / (logInput5 - logInput2);

    // Detect exponential time
    // When log-log plot has increasing slope
    // Values chosen not to overflow long
    double slopeLogLogX = (Math.log(fn.apply(7L)) - logOutput5) / (Math.log(7) - logInput5);
    if (floatLessThan(slopeLogLog1, slopeLogLogX)) {
      return String.format("O(n^n)");
    }

    Long input25 = Long.valueOf(25);
    Long output25 = fn.apply(input25);
    double logOutput25 = Math.log(output25.doubleValue());
    double logInput25 = Math.log(input25.doubleValue());
    double slopeLogLog2 = (logOutput25 - logOutput5) / (logInput25 - logInput5);

    // Detect constant time
    if (output5 == output25) {
      return "O(1)";
    }

    // Detect less than O(n) and assume log(n)
    if (floatLessThan(slopeLogLog2, 1)) {
      return ("O(log(n))");
    }

    // Detect linear time
    if (floatEquality(slopeLogLog1, 1)) {
      return "O(n)";
    }

    // Detect polynomial time
    if (floatEquality(slopeLogLog1, slopeLogLog2)) {
      return String.format("O(log(n^%d))", Math.round(Math.ceil(slopeLogLog1)));
    }

    return "Unknown";
  };

  static boolean floatEquality(double a, double b) {
    return Math.abs(a - b) < 0.0005;
  }

  static boolean floatLessThan(double a, double b) {
    return a - b < -0.0005;
  }
}
