# Test times
```java
import java.text.SimpleDateFormat;
import java.util.*;
import java.util.stream.Collectors;

public class TimeSortTest {
    private static final long SECOND_IN_MILLIS = 1000L;
    private static final long HOUR_IN_MILLIS = 60L * 60L * SECOND_IN_MILLIS;
    private static final long DAY_IN_MILLIS = 24 * HOUR_IN_MILLIS;

    private static final Comparator<Long> COMPARATOR = (x, y) -> ((int) (y - x));

    private final Random mRandom = new Random();

    private int mReminderCount = 35;

    private int mProbabilityTestCount = 1000000;

    private int mDayExpandingCount;

    private long mStartGeneratingTime;

    private int mHourOfGenGuild = 8;

    private int mMinuteOfGenGuild = 0;

    private boolean mIsPrint = false;

    public TimeSortTest() {
        setDayExpandingCount(30);
    }

    public void setReminderCount(int reminderCount) {
        mReminderCount = reminderCount;
    }

    public void setProbabilityTestCount(int probabilityTestCount) {
        mProbabilityTestCount = probabilityTestCount;
    }

    public void setDayExpandingCount(int dayExpandingCount) {
        mDayExpandingCount = dayExpandingCount;
        mStartGeneratingTime = System.currentTimeMillis() - DAY_IN_MILLIS * mDayExpandingCount;
        mStartGeneratingTime -= mStartGeneratingTime % HOUR_IN_MILLIS;
    }

    public void setMonthExpandingCount(int monthExpandingCount) {
        setDayExpandingCount(monthExpandingCount * 30);
    }

    public void setHourOfGenGuild(int hourOfGenGuild) {
        mHourOfGenGuild = hourOfGenGuild;
    }

    public void setMinuteOfGenGuild(int minuteOfGenGuild) {
        mMinuteOfGenGuild = minuteOfGenGuild;
    }

    public void testProbability() {
        System.out.println("Probability: " + calculateProbability() + " %");
    }

    private float calculateProbability() {
        int count = 0;
        for (int i = 0; i < mProbabilityTestCount; i++) {
            if (checkTimes(generateTimes(this::genNextTimeProbably))) {
                count++;
            }
        }
        return (float) count * 100 / mProbabilityTestCount;
    }

    private long genNextTimeProbably() {
        return mStartGeneratingTime + randomDay() * DAY_IN_MILLIS;
    }

    public void showGuide() {
        System.out.println("Turn off network and clear reminder data");
        System.out.println("Change time to " + getCreatedTime());
        System.out.println("Create " + mReminderCount + " reminders with titles: 1 -> " + mReminderCount + " (By quick add)");
        List<Long> times = genTest();
        Map<Long, List<Integer>> map = new HashMap<>();
        for (int i = 0; i < times.size(); i++) {
            long time = times.get(i);
            if (map.containsKey(time)) {
                map.get(time).add(i + 1);
            } else {
                List<Integer> indexes = new ArrayList<>();
                indexes.add(i + 1);
                map.put(time, indexes);
            }
        }
        new ArrayList<>(map.keySet()).stream().sorted(Long::compareTo)
                .forEach(time -> map.get(time).forEach(index ->
                        System.out.println("Change time to " + getTimeText(time) + " and modify title of Reminder: " + index)));
        System.out.println("Select all reminder and delete");
        System.out.println("Go to Trash");
    }

    private String getCreatedTime() {
        return getTimeText(getStartTimeGuide().getTimeInMillis() - DAY_IN_MILLIS);
    }

    private List<Long> genTest() {
        while (true) {
            List<Long> testTimes = generateTimes(this::genNextTimeWithSameHour);
            List<Long> saved = new ArrayList<>(testTimes);
            if (checkTimes(testTimes) && validBoundaryTest(saved)) {
                return saved;
            }
        }
    }

    private long genNextTimeWithSameHour() {
        return getStartTimeGuide().getTimeInMillis() + randomDay() * DAY_IN_MILLIS;
    }

    private Calendar getStartTimeGuide() {
        Calendar calendar = Calendar.getInstance();
        calendar.setTimeInMillis(mStartGeneratingTime);
        calendar.set(Calendar.DAY_OF_MONTH, 1);
        calendar.set(Calendar.HOUR, mHourOfGenGuild);
        calendar.set(Calendar.MINUTE, mMinuteOfGenGuild);
        return calendar;
    }

    private boolean validBoundaryTest(List<Long> times) {
        for (long i = 0; i < 10000; i++) {
            List<Long> testTimes = new ArrayList<>(times).stream()
                    .map(time -> time + (mRandom.nextBoolean() ? 0 : 60))
                    .collect(Collectors.toList());
            if (!checkTimes(testTimes)) {
                print("{ Random not pass the boundary test }");
                print("{ Invalid time " + i + " }");
                print("{ Trying with other random... }");
                return false;
            }
        }
        return true;
    }

    private int randomDay() {
        return mRandom.nextInt(mDayExpandingCount);
    }

    private static boolean checkTimes(List<Long> times) {
        try {
            times.sort(COMPARATOR);
        } catch (IllegalArgumentException e) {
            return true;
        }
        return false;
    }

    private static String getTimeText(long time) {
        Date date = new Date(time);
        return new SimpleDateFormat("yyyy/MM/dd hh:mm:ss")
                .format(date);
    }

    private List<Long> generateTimes(TimeGenerator timeGenerator) {
        List<Long> list = new ArrayList<>();
        for (int i = 0; i < mReminderCount; i++) {
            list.add(timeGenerator.genTime());
        }
        return list;
    }


    public void setIsPrint(boolean isPrint) {
        mIsPrint = isPrint;
    }

    private void print(String text) {
        if (mIsPrint) {
            System.out.println(text);
        }
    }

    private interface TimeGenerator {
        long genTime();
    }
}

```
