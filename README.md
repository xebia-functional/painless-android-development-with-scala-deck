footer: (Scala on Android) ⇒ Painless Android Development with Scala 
slidenumbers: true

![Scala on Android, The current state of affairs](https://raw.githubusercontent.com/47deg/painless-android-development-with-scala-deck/master/assets/01@2x.png)

---
⇒ 47 Degrees, a global consulting agency & Typesafe Consulting Partner.

![inline](https://raw.githubusercontent.com/47deg/scala-on-android-deck/master/assets/47.png)

@raulraja
@javielinux
@47deg
http://47deg.com/blog

---

Build Tools

![sbt, gradle & maven](https://raw.githubusercontent.com/47deg/scala-on-android-deck/master/assets/02@2x.png)

---

# [fit] SBT ⇒ Android SDK Plugin

https://github.com/pfn/android-sdk-plugin

---

# [fit] SBT ⇒ Android SDK Plugin

- Supports all Android SDK tasks
    - dex 
    - _**typedResourcesGenerator**_ 
    - _**proguard**_
    - buildConfigGenerator
    - (+ 20... more)
                    
---

# SBT ⇒ Android SDK Plugin ⇒ **`typedResourcesGenerator`**

```scala

object TR {
  val title = TypedResource[TextView](R.id.title)

  object layout {
    val abc_screen_toolbar = TypedLayout[ActionBarOverlayLayout](R.layout.abc_screen_toolbar)
  }
}

class MyActivity extends AppCompatActivity with TypedActivity {

  val titleTextView = findView(title) //titleTextView inferred as TextView, no casting needed

}

```

---

### SBT ⇒ Android SDK Plugin ⇒ _**`proguard`**_

## [fit] Size Matters

## [fit] Scala byte code size reduced ~ (2.8M)

---

# SBT ⇒ Android SDK Plugin

- _**`https://github.com/pfn/android-sdk-plugin`**_
- Active
- Fast (incremental compilation and proguard caching)
- Proguard + MultiDexApplication integration 
  _(Circumvents 65K method limit)_
- Supports *AAR*, *JAR* and *APK* artifact types

---

# Java Vs Scala ⇒ _**`NullPointerExceptions`**_

```java
Person person = getPerson();

String name = null;

if (person != null && person.getJob() != null) {
  name = person.getJob().getName();
}

if (name != null) {
  return name;
} else {
  return DEFAULT_NAME;
}
```
---

# Java Vs Scala ⇒ _**`NullPointerExceptions`**_

```scala
val jobName : Option[String] = person.job map (_.name)
```
OR

```scala
val jobName : Option[String] = for {
   job <- person.job
} yield job.name
```
---

# Java Vs Scala ⇒ _**`NullPointerExceptions`**_

```java
Person person = getPerson();

String name = null;

if (person != null && person.getJob() != null) {
  name = person.getJob().getName();
}

if (name != null) {
  return name;
} else {
  return DEFAULT_NAME;
}
```
---

# Java Vs Scala ⇒ _**`NullPointerExceptions`**_

```scala
val jobName : Option[String] = person.job map (_.name)
```

*OR*

```scala
val jobName : Option[String] = for {
   job <- person.job
} yield job.name
```

*OR*

`Option`, `Try`, `Either`, `\/`, `Validation`

---

# Java Vs Scala ⇒ _**`Contexts`**_

```java
public class MainActivity
    extends Activity {

  public void bar() {
    FooUtils.get(getContext(), R.string.name);
  }

}


public class FooUtils {
  static String get(Context c, int res) {
     return c.getString(res);
  } 
}
```
---

# Java Vs Scala ⇒ _**`Contexts`**_

```scala
class MainActivity extends Activity {

   implicit val ctx = getApplicationContext

   def bar = FooUtils.get(R.string.name)

}

object FooUtils {
  def get(res : Int)(implicit ctx : Context) = {
     ctx.getString(res)
  } 
}

```
---

# Java Vs Scala ⇒ _**`Contexts`**_

```scala
trait Contexts { self : Activity =>

    implicit val appContext = getApplicationContext
    
    implicit val activityContext = this

}

class MainActivity extends Activity with Contexts {

   def bar = FooUtils.get(R.string.name)

}

object FooUtils {
  def get(res : Int)(implicit ctx : Context) = {
     ctx.getString(res)
  } 
}

```
---

# Java Vs Scala ⇒ _**`Implicit Classes`**_

```java
public class TextViewUtils {

    public void loadFont(TextView textView, String font) {
        textView.setFontType(font,...)
    }
}

TextViewUtils.loadFont(textView, "robotto");

```
---

# Java Vs Scala ⇒ _**`Implicit Classes`**_

```scala
object Helpers {
  implicit class TextViewHelpers(textView: TextView) {
    def loadFont(font: String) = ???
  }
}

import Helpers._

textView.loadFont(“Roboto.ttf”)

```
---
# Java Vs Scala ⇒ _**`Async`**_

```java
public class MyTask1 extends AsyncTask<Void, Void, Integer> {
    protected Integer doInBackground(Void... v) {
      return r1;
    }

    protected void onPostExecute(Integer result) {
      new MyTask2().execute(result);
    }
 }

public class MyTask2 extends AsyncTask<Integer, Void, Integer> {
    protected Integer doInBackground(Integer... r1) {
      return r2;
    }

     protected void onPostExecute(Integer result) {
         r1 + r2;
     }
 }

new MyTask1().execute();

```
---
# Java Vs Scala ⇒ _**`Async`**_

```scala
def myTask1: Future[Int] = Future(1)

def myTask2: Future[Int] = Future(2)

def sumResponses: Future[Int] = 
  for {
    r1 <- myTaks1
    r2 <- myTaks2
  } yield (r1 + r2)

sumResponses map println
```
---
# Java Vs Scala ⇒ _**`Async`**_

```scala
Future.reduce(List(Future(1), Future(2))) { (a, b) =>
    a + b
}
```
---
# Java Vs Scala ⇒ _**`Async`**_

```scala
Future.reduce(List(Future(1), Future(2))) (_ + _)
```
![inline](http://fc03.deviantart.net/fs70/f/2012/137/1/b/boomer_trollface_by_blackrhinoranger-d503d1o.png)

---
# Java Vs Scala ⇒ _**`Models`**_

```java
public class Person {
    
    private String name;
    
    private String lastName;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getLastName() {
        return lastName;
    }

    public void setLastName(String lastName) {
        this.lastName = lastName;
    }
}
```

![right fit](http://i40.tinypic.com/ziooyv.gif)

---

# Java Vs Scala ⇒ _**`Models`**_

```scala
case class Person(
  name : String, 
  lastName : String)
```

![right fit](http://img.pandawhale.com/58719-Danaerys-Targaryen-sunglasses-1tNO.jpeg)

---

# Java Vs Scala ⇒ _**`Pattern Matching`**_

```java
public boolean onTouchEvent(MotionEvent ev) {
  ...
  switch (action) {
      case MotionEvent.ACTION_DOWN:
          ...
          break;
      case MotionEvent.ACTION_MOVE:
          ...
          break;
      case MotionEvent.ACTION_UP:
      case MotionEvent.ACTION_CANCEL:
          ...
          break;
  }
}
```
---
# Java Vs Scala ⇒ _**`Pattern Matching`**_

```scala
def onTouchEvent(ev: MotionEvent) {
  import MotionEvent._
  action match {
     case ACTION_DOWN => ???
     case ACTION_MOVE => ???
     case ACTION_UP | ACTION_CANCEL => ???
  }
}
```
---
# Java Vs Scala ⇒ _**`Pattern Matching`**_

```scala
person match {
  case Person(_, lastName) if lastName == “Pacheco” => 
    println(“Guapetón”)
  case Person(name, _) if name == “Raúl” =>
    println(“Resultón”)
  case _ => 
    println(“Programadores Java”)
}
```
---
# Java Vs Scala ⇒ _**`Singletons`**_

```java
public class MySingleton {

  private MySingleton(){}

  public synchronized static MySingleton getInstance() {   
        if(instance == null) instance = new MySingleton ();
        return instance;
  }

  public void bar(){
    ...
  }
}
```
---
# Java Vs Scala ⇒ _**`Singletons`**_

```scala
object MySingleton {
  def bar = ...
}
```
---
# Java Vs Scala ⇒ _**`Mixins`**_

```java
class Mammal

class PlatyPus extends Mammal

new PlatyPus().eggs() <- Compilation Error!!!
```
![right fit](http://upload.wikimedia.org/wikipedia/commons/e/e0/Wild_Platypus_4.jpg)

---
# Java Vs Scala ⇒ _**`Mixins`**_

```scala
trait Mammal

trait EggsSupport {
  def eggs : Eggs
}

class PlatyPus extends Mammal with EggsSupport

PlatyPus().eggs <- Success!!!
```
![right fit](http://upload.wikimedia.org/wikipedia/commons/e/e0/Wild_Platypus_4.jpg)

---

# OS Apps ⇒ _**`Translate Bubble`**_

### https://play.google.com/store/apps/details?id=com.fortysevendeg.translatebubble

### https://github.com/47deg/translate-bubble-android

![inline](https://raw.githubusercontent.com/47deg/scala-on-android-deck/master/assets/translatebubble.png)

---

# OS Apps ⇒ _**`Scala Days Official App`**_

### https://play.google.com/store/apps/details?id=com.fortysevendeg.android.scaladays

### https://github.com/47deg/scala-days-android 

![inline](https://raw.githubusercontent.com/47deg/scala-on-android-deck/master/assets/scaladays.png)

---

# OS Apps ⇒ _**`Scala API Demos`**_

### https://play.google.com/store/apps/details?id=com.fortysevendeg.scala.android

### https://github.com/47deg/scala-android

![inline](https://raw.githubusercontent.com/47deg/scala-on-android-deck/master/assets/scala-api-demos.png)

---

# Thank you

@47deg

@javielinux

@raulraja

http://47deg.com/blog

## https://speakerdeck.com/raulraja/painless-android-development-with-scala-deck

## https://github.com/47deg/painless-android-development-with-scala-deck


