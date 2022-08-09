# Test times
```java
    private static final Comparator<Long> COMPARATOR = (x, y) -> ((int) (y - x));
    private static final boolean IS_PRINT = true;
    private static final Random sRandom = new Random();

    public static void main(String[] args) {
        genTest();
    }

    private static void testProbability() {
        final int n = 100;
        float sum = 0;
        for (int i = 0; i < n; i++) {
            float probability = calculateProbability(1000);
//            print(probability + "");
            sum += probability;
        }
        float avg = sum / n;
        print("AVG: " + avg);
    }

    private static float calculateProbability(int timesCount) {
        int count = 0;
        for (int i = 0; i < timesCount; i++) {
            if (checkTimes(generateTimes())) {
                count++;
            }
        }
        return (float) count / timesCount;
    }

    private static void genTest() {
        int i = 0;
        while (true) {
            i++;
            print("Test " + i);
            List<Long> testTimes = generateTimes();
            List<Long> saved = new ArrayList<>(testTimes);
            if (checkTimes(testTimes)) {
                boolean isPassed = true;
                for (int j = 0; j < 100; j++) {
                    testTimes = new ArrayList<>(saved);
                    testTimes = testTimes.stream()
                            .map(time -> time + sRandom.nextInt(5 * 1000 * 60))
                            .collect(Collectors.toList());
                    if (checkTimes(testTimes)) {
                        isPassed = false;
                    }
                }
                if (isPassed) {
                    printTimes(saved);
                    break;
                }
            }
        }
    }

    private static boolean checkTimes(List<Long> times) {
        try {
            times.sort(COMPARATOR);
        } catch (IllegalArgumentException e) {
            return true;
        }
        return false;
    }

    private static List<Long> generateTimes() {
        List<Long> list = new ArrayList<>();
        for (int i = 0; i < 35; i++) {
            list.add(nextTime(sRandom.nextInt(30)));
        }
        return list;
    }

    private static void printTimes(List<Long> times) {
        for (Long time: times) {
            print(getTimeText(time));
        }
    }

    private static String getTimeText(long time) {
        Date date = new Date(time);
        return new SimpleDateFormat("yyyy/MM/dd hh:mm:ss")
                .format(date);
    }

    private static long nextTime(int day) {
        Calendar calendar = Calendar.getInstance();
        calendar.add(Calendar.MONTH, -1);
        calendar.set(Calendar.DAY_OF_MONTH, 1);
        calendar.add(Calendar.DAY_OF_MONTH, day - 1);
        calendar.set(Calendar.HOUR_OF_DAY, 8);
        calendar.set(Calendar.MINUTE, 0);
        calendar.set(Calendar.SECOND, 0);
        return calendar.getTimeInMillis() - calendar.getTimeInMillis() % 1000;
    }

    private static void print(String text) {
        if (IS_PRINT) {
            System.out.println(text);
        }
    }
```
