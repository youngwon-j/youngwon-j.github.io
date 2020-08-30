---
layout: post
title: LinkedHashMap 분석 및 HashMap 과의  Performance 비교
---

Map interface를 구현한 LinkedHashMap은 HashMap의 기능은 유지하되, 순서가 보장되는 Map으로 알려져 있다.
LinkedHashMap의 주요 특징으로는

1. LinkedHashMap 은 HashMap을 상속하고
2. LinkedHashMap은 순서를 유지하기 때문에 HashMap과 비교하여 당연히 더 많은 메모리를 필요로 하는것으로 알려져있다.

순서를 보장하기 위해 LinkedHashMap을 사용중 LinkedHashMap의 작동원리와, HashMap과의 Performance의 차이가 궁금해져 이번 포스팅을 준비하였다.

*순수히 공부를하며 쓴 글이기에 부족한 점이 많으므로, 수정할 점이 있다면 댓글 달아주셨으면 좋겠습니다.^^



LinkedHashMap은 아래의 다섯가지 생성자를 제공한다.

{% highlight ruby %}
   public LinkedHashMap(int initialCapacity, float loadFactor) {
        super(initialCapacity, loadFactor);
        accessOrder = false;
}
{% endhighlight %}
{% highlight ruby %}
 public LinkedHashMap(int initialCapacity) {
        super(initialCapacity);
        accessOrder = false;
    }
{% endhighlight %}


{% highlight ruby %}
  public LinkedHashMap() {
        super();
        accessOrder = false;
    }
{% endhighlight %}

{% highlight ruby %}
   public LinkedHashMap(Map<? extends K, ? extends V> m) {
        super(m);
        accessOrder = false;
    }
{% endhighlight %}

{% highlight ruby %}
 public LinkedHashMap(int initialCapacity,
                         float loadFactor,
                         boolean accessOrder) {
        super(initialCapacity, loadFactor);
        this.accessOrder = accessOrder;
    }
{% endhighlight %}


몇가지 파라미터에 대해 설명하자면

1) capacity는 map의 크기, 용량이며 (default 16)

2) accessOrder 는  ordering mode 를 규정하는 파라미터로써
true 일 때는 access-order, false일 때는 insertion-order로 순서를 저장한다.

3) loadFactor란, 간단히 말하면 해당 인스턴스의 capacity가 얼마나 차게 되면, 용량을 늘려야할 것인지에 대한 수치이다.
(default 0.75)
생성자를 보면 accessOrder 값은 false가 default값이며, 이는 즉 삽입 순서가 정렬 순서가 됨을 뜻한다.


HashMap의 기본 생성자는 다음과 같고

{% highlight ruby %}
 public HashMap() {
        this(DEFAULT_INITIAL_CAPACITY, DEFAULT_LOAD_FACTOR);
    }
{% endhighlight %}

최종적으로는 아래의 코드가 수행된다.

{% highlight ruby %}
   public HashMap(int initialCapacity, float loadFactor) {
        if (initialCapacity < 0)
            throw new IllegalArgumentException("Illegal initial capacity: " +
                                               initialCapacity);
        if (initialCapacity > MAXIMUM_CAPACITY)
            initialCapacity = MAXIMUM_CAPACITY;
        if (loadFactor <= 0 || Float.isNaN(loadFactor))
            throw new IllegalArgumentException("Illegal load factor: " +
                                               loadFactor);

        this.loadFactor = loadFactor;
        threshold = initialCapacity;
        init();
    }
{% endhighlight %}

파라미터로 넘어온 loadFactor , initialCapacity를 체크 후 init메서드를 호출하는 것을 볼 수 있다.

아무것도 수행하지 않는 아래의 HashMap의 init메서드와 달리
{% highlight ruby %}
  void init() {
    }
 {% endhighlight %}

LinkedHashMap의 init메서드는 아래 같이 순서를 보장하기 위해 header 필드를 초기화 하는 것을 볼 수 있다.

{% highlight ruby %}
 void init() {
        header = new Entry<>(-1, null, null, null);
        header.before = header.after = header;
    }
{% endhighlight %}


put 메서드를 살펴보겠다.

{% highlight ruby %}
public V put(K key, V value) {
        if (table == EMPTY_TABLE) {
            inflateTable(threshold);
        }
        if (key == null)
            return putForNullKey(value);
        int hash = hash(key);
        int i = indexFor(hash, table.length);
        for (Entry<K,V> e = table[i]; e != null; e = e.next) {
            Object k;
            if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
                V oldValue = e.value;
                e.value = value;
                e.recordAccess(this);
                return oldValue;
            }
        }

        modCount++;
        addEntry(hash, key, value, i);
        return null;
    }
{% endhighlight %}

위는 HashMap의 put 메서드이다.
LinkedHashMap은  HashMap의 put 메서드를 오버라이딩 하지 않는다.
HashMap의 put메서드는,  hash값 및 인덱스를 초기화 한 후에
addEntry를 호출한다.

LinkedHashMap의 addEntry 메서드
{% highlight ruby %}
 void addEntry(int hash, K key, V value, int bucketIndex) {
        super.addEntry(hash, key, value, bucketIndex);

        // Remove eldest entry if instructed
        Entry<K,V> eldest = header.after;
        if (removeEldestEntry(eldest)) {
            removeEntryForKey(eldest.key);
        }
    }
{% endhighlight %}

HashMap의 addEntry 메서드
{% highlight ruby %}
   void addEntry(int hash, K key, V value, int bucketIndex) {
        if ((size >= threshold) && (null != table[bucketIndex])) {
            resize(2 * table.length);
            hash = (null != key) ? hash(key) : 0;
            bucketIndex = indexFor(hash, table.length);
        }

        createEntry(hash, key, value, bucketIndex);
    }
{% endhighlight %}

LinkedHashMap의 createEntry 메서드
{% highlight ruby %}
 void createEntry(int hash, K key, V value, int bucketIndex) {
        HashMap.Entry<K,V> old = table[bucketIndex];
        Entry<K,V> e = new Entry<>(hash, key, value, old);
        table[bucketIndex] = e;
        e.addBefore(header);
        size++;
    }
 {% endhighlight %}


{% highlight ruby %}
private void addBefore(Entry<K,V> existingEntry) {
    after  = existingEntry;
    before = existingEntry.before;
    before.after = this;
    after.before = this;
}
{% endhighlight %}



위의 흐름에서, addEntry 메서드를 호출 시

1) 새로운 Entry를 생성하여 ( createEntry )

2)  linked list를 통해 순서를 유지하는 것 ( addBefore )

을 볼 수 있다.


값을 읽어오는 get 메서드는
{% highlight ruby %}
public V get(Object key) {
        Entry<K,V> e = (Entry<K,V>)getEntry(key);
        if (e == null)
            return null;
        e.recordAccess(this);
        return e.value;
    }
{% endhighlight %}
{% highlight ruby %}
 final Entry<K,V> getEntry(Object key) {
        if (size == 0) {
            return null;
        }

        int hash = (key == null) ? 0 : hash(key);
        for (Entry<K,V> e = table[indexFor(hash, table.length)];
             e != null;
             e = e.next) {
            Object k;
            if (e.hash == hash &&
                ((k = e.key) == key || (key != null && key.equals(k))))
                return e;
        }
        return null;
    }
{% endhighlight %}
기존의 HashMap과 같이 hash값을 이용하여 값을 가져 온 후 recordAccess메서드를 호출한다.

{% highlight ruby %}
 void recordAccess(HashMap<K,V> m) {
            LinkedHashMap<K,V> lm = (LinkedHashMap<K,V>)m;
            if (lm.accessOrder) {
                lm.modCount++;
                remove();
                addBefore(lm.header);
            }
        }
{% endhighlight %}
recordAccess 메서드는 인스턴스 생성 시 정한 accessOrder(boolean)에 따른 정렬을 지원한다.


Performance 테스트 결과

테스트는 각 인스턴스 생성 후 put, get 메서드를 수회 반복한 후 수행시간을 측정하였으며
제 3요인의 영향을 줄이기 위하여  각 회차당 10회씩 측정하여 그 결과값의 평균치로 그래프를 그렸다.

![comparison_of_put.png](/assets/comparison_of_put.png)
[그림1] put 메소드 수행결과

Y 축이 시간임을 감안할 때, LinkedHashMap이 HashMap과 비교하여 put 메서드를 수행하는 시간이 조금 더 긴 것을 알 수 있다.


![comparison_of_get.png](/assets/comparison_of_get.png)
[그림2] get 메소드 수행 결과

Y 축이 시간임을 감안할 때, LinkedHashMap은 HashMap과 비교하여 get 메서드를 수행하는 시간이 비슷함을 알 수 있다.




퍼포먼스 테스트 결과, LinkedHashMap 과 HashMap 퍼포먼스에 상당한 차이가 있을거라고 예상했으나 실제 측정결과는 달랐다.
퍼포먼스에 있어 뚜렷한 성능 차이를 보이지 못했으며 특히 반복횟수가 작을 경우 (100 - 1000회) 는 거의 차이가 없음을 확인했다.


