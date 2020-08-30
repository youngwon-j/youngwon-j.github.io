---
layout: post
title: Facade 패턴
---

Facade패턴

Provide a unified interface to a set of interfaces in a subsystem. Facade defines a higher-level interface that makes the subsystem easier to use. ( Design pattern - Bob Tarr)

Facade 패턴은 '통합된 인터페이스'를 제공한다.
즉 복잡한 서브 시스템을 '간단한 인터페이스'로 바꿔 개발자로 하여금 좀 더 쉽게 복잡한 서브시스템을 사용할 수있게 한다는 것이 그 골자다.

아래 포스팅에서 확인할 수 있듯, 이 Facade 패턴을 사용하면 독자적 단위 기반의 서브시스템을 통합한 Facade패턴을 사용하면 복잡한 시스템에서 기능변경이 일어났을 때 여러 부분들을 연속으로 고치지 않고 해당 부분만 간단히 고쳐 해결할 수 있음을 확인할 수 있을 것이다.

Facade.
사전을 찾아보니 '정면'이라는 뜻이다. 여러 서브시스템을 호출해주는 역할을 하는 '정면'만을 호출함으로써 복잡한 기능을 수행한다 라는 뜻이 아닐까 한다.
해당 패턴을 처음 접한 것은 인터페이스 기반의 Library 설명문서를 읽으면서이다.
처음에는 단어 자체가 생소해서 뭔가 복잡하고 어려운 패턴인줄 알았지만, 막상 공부해보니 아주 간단한 패턴 중 하나이다.
Facade패턴에 대해 알아보도록 하자 :)

*순수히 공부를하며 쓴 글이기에 부족한 점이 많으므로, 수정할 점이 있다면 댓글 달아주셨으면 좋겠습니다.^^




쉽게 예제를 들어 설명하곘다.
이번 예제에서는 자동차의 시동을 켜고, 끄는 간단한 기능을 Facade패턴으로 구현해보겠다.



{% highlight ruby %}
public class CarFacadePattern {

 Engine engine;
 Gear gear;
 Light light;
 RadioSystem radioSystem;
 Break break;

 public CarFacadePattern(Engine engine, Gear gear, RadioSystem radioSystem, Light light, Break break) {
     this.engine = engine;
     this.gear = gear;
     this.light = light;
     this.RadioSystem = radioSystem;
     this.break = break;

 }

  {% endhighlight %}


위의 코드에서 볼 수 있듯, 클래스 생성자의 파라메터로 여러 클래스들을 받고 있음을 알고 있다.
이 클래스들은 각각의 서브시스템을 구성하는 단위이다.
생성자에서는, 해당 파라미터들을 미리 선언된 멤버필드의 변수들에게 할당한다.


{% highlight ruby %}
 public void startCar() {
  if(gear.checkGear(GEAR_PARKING) && break.checkBreak(BREAK_ON)){
      engine.on();
      light.on();
      light.setDefault();
      radioSystem.on();
      radioSystem.setDefault();
  };
 }
  {% endhighlight %}
시동을 켜는 일련의 과정을 함수로 만들어 보겠다.
간단한 예를 들어, 시동을 켜기 위해서는
1. 기어를 체크하고
2. 엔진을 켜고
3. 라이트를 켜고
4. 라디오 시스템을 작동시킨다고 정의하겠다.

{% highlight ruby %}
 public void endCar() {

     light.off();
     if(gear.checkGear(GEAR_PARKING)){
         light.off();
         radioSystem.off();
         engine.off();
     }else{
         alert("please set gear P");
     }
 }
   {% endhighlight %}
시동을 끄는 일련의 과정을 함수로 만들어 보겠다.
시동을 켜는것과 같이, Light, Radiosystem, Engine을 끄는 순서로 진행하겠다.

위의 코드를 통해 여러분들은 Facade 패턴을 구현한것이다.
설명하자면

해당 시동을 켜고 끄는부분은
 {% highlight ruby %}
 BmwEngine bmwEngine = new BmwEngine();
 BmwGear bmwGear = new BmwGear();
 BoseRadioSystem boseRadioSystem = new BoseRadioSystem();
 PhilipsLight philipsLight = new PhilipsLight();
 BmwBreak bmwBreak = new BmwBreak();

  CarFacadePattern carFacadePattern = new CarFacadePattern(bmwEngine, bmwGear, boseRadioSystem, philipsLight, bmwBreak);
  carFacadePattern.startCar();
  ...
  carFacadePattern.endCar();
   {% endhighlight %}
로 호출할 수 있다.

이 때, 파라메터로 전달될 각 클래스들은
Engine, Gear, Radio, Light, Break 인터페이스를 구현한 구현체이다.
예를 들면
{% highlight ruby %}
public class FacadePattern implements Engine{
    @override
    public void on(){
        //...BMW 엔진 점화과정
    }
}
 {% endhighlight %}

식으로 구현될 수 있다.

이처럼, 여러 복잡한 서브시스템들을 통합하는것이 ' FACADE 패턴이다'
인터페이스를 이해하고 있다면, 간단한 패턴임을 알 수 있다.

