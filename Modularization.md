# Modularization

[Smell](..) → [Girish Suryanarayana Code Smells](Girish) → [Modularization](#)

![Girish modularization smell](img/girish/modularization.png "Girish modularization smell")

Semua smell di dalam grup ini berkaitan dengan kesalahan dalam merancang modul-modul di dalam hirerarki.

- [Broken Modularization](#broken-modularization)
- [Insufficient Modularization](#insufficient-modularization)
- [Cyclically-dependent Modularization](#cyclically-dependent-modularization)
- [Hub-like Modularization](#hub-like-modularization)

### Filosofi

Buku adalah konten yang berisikan sekumpulan teks paragraf. Bayangkan apa yang terjadi bila semua teks ditulis mentah-mentah tanpa menggunakan Heading, Chapter, Bab, Indeks maupun sub-bab untuk membatasi isi materi dalam buku? Jawabannya pasti akan mempersulit pembaca lain dalam memahami materi dari buku yang mereka buat tentunya.

### Prinsip Modularization

[Link Video](https://www.youtube.com/watch?v=Q5Y42rdZza4&list=PLG_Cu5FmqSk2KHT6lXngRvcOmOzuk4_ju)

![Girish Modularization principles](img/girish/modularization_principles.png "Girish modularization principles")

Secara teoretis, Sebuah class/abstraksi seharusnya memiliki tingkat kohesi yang tinggi dan coupling yang rendah sehingga class tersebut mempunyai tanggungjawab yang sesuai, function dan member yang saling berinteraksi satu sama lain tanpa bergantung pada class lain.

Menurut Girish Suryanarayana dkk, terdapat 4 prinsip modularization yaitu:

- **Localize related data and methods** - Kumpulkan field, member, dan method yang mempunyai tanggungjawab yang sama ke dalam 1 abstraksi.
- **Decompose abstractions to manageable size** - Membagikan sebuah abstraksi yang terlalu besar menjadi beberapa abstraksi (yang imbang ukurannya, tidak terlalu kecil dan tidak terlalu besar) yang disesuaikan dengan tanggungjawab masing-masing abstraksi/class sehingga dapat dimengerti oleh developer.
- **Create acyclic dependencies** - Sebuah abstraksi seharusnya tidak boleh mengandung dependensi yang *cyclic* dengan class lain baik secara langsung ataupun tidak langsung. Jika digambarkan dalam *dependency graph*, maka graph tersebut tidak boleh terdapat perputaran dependency antar-class. Prinsip ini tentunya bertujuan agar tidak terjadi *domino-effect* ataupun [*shotgun-surgery*](Change-Preventers#shotgun-surgery) karena adanya perubahan dalam satu modul.
- **Limit dependencies** - Sebuah abstraksi seharusnya dibuat dengan dependensi seminimal mungkin baik *fan-in* (import) maupun *fan-out* (usages for another abstraction). Tujuan dari prinsip tersebut sama dengan *Create acyclic dependencies* yaitu untuk mencegah terjadinya *domino-effect* ataupun [*shotgun-surgery*](Change-Preventers#shotgun-surgery) karena adanya perubahan dalam satu modul pelanggar.

Berdasarkan pada pengamatan abstraction smell, terdapat pelanggaran prinsip encapsulation antara lain:
(field dan method)

| Modularization smell | Pelanggaran prinsip modularization | Penyebab | Martin Fowler smells |
| --- | --- | --- | --- |
| Broken Modularization | Localize related data and methods | 1. Pemisahan data dan method dalam class terpisah<br>2. Method kecolongan bermain dengan class lain | 1. Data Class<br>2. Feature Envy |
| Insufficient Modularization | Decompose abstractions to manageable size | 1. Class terlalu besar, tidak ada modularization<br>2. Class/method terlalu kompleks | 1. Large class, divergent changes<br>2. Long method |
| Cyclically-dependent Modularization | Create acyclic dependencies | Class saling dependensi satu sama lain dengan pasangannya atau teman *selingkarnya* | Shotgun Surgery, Feature Envy, Inappropriate Intimacy |
| Hub-like Modularization | Limit dependencies | Class terlalu banyak dependensi dari class lain atau dependensi masukan dari class lain | Shotgun Surgery (bisa terjadi pada class yang depend dengannya) |


## Broken Modularization

[Link Video](https://www.youtube.com/watch?v=0aeIbhESMco&list=PLG_Cu5FmqSk2KHT6lXngRvcOmOzuk4_ju) |
[Materi](../tree/master/src/girish/modularization/broken)

Smell ini terjadi jika pada data member, field, atau method yang seharusnya dikumpulkan dalam 1 class/abstraksi malah terpisah dan tersebar di abstraksi lain. Smell ini sering dimanifestasikan sebagai:

- Class yang hanya menampung data tapi tidak ada method yang memperlakukan data/berinteraksi dalam class. Smell ini disamaratakan dengan [Data Class](Dispensables#data-class).
- Method yang senang bermain dengan member lain di class lain. Smell ini disamaratakan dengan [Feature Envy](Couplers#feature-envy).

### Penyebab

- **Procedural thinking in object-oriented languages**: Disebabkan karena developer procedural programming languages mengasumsikan bahwa data harus dipisahkan dari function-function yang memproses data tersebut sehingga dalam OOP, developer memecahkannya ke class terpisah.
- **Lack of knowledge of existing design**: Dalam beberapa kasus terutama dalam perusahaan besar, terdapat banyak class yang seharusnya dapat developer kerjakan selain dari bagian yang ia kerjakan. Hal itu kurang diketahui olehnya sehingga developer menempatkan member/method pada lokasi yang kurang tepat yang pada akhirnya menimbulkan smell tersebut.

### Penyelesaian

![Refactoring move field/member/method](img/girish/modularization/broken-solution.png "Refactoring move field/member/method")

Cara paling mudahnya dalam menyelesaikan smell ini adalah dengan memindahkan member-member dari kelas yang terpisah ke class asal-nya. Jika kasus tersebut terjadi pada:

- Method class tersebut sering dipakai oleh class lain, pindahkan method class tersebut ke dalam method class pemakainya dengan ["move field"](https://refactoring.guru/move-field).
- Member class tersebut sering dipakai oleh class lain, pindahkan member class tersebut ke dalam member class pemakainya dengan ["move method"](https://refactoring.guru/move-method).

### Contoh

![Struktur class dalam aplikasi device management](img/girish/modularization/broken-1.png "Struktur class dalam aplikasi device management")

#### Masalah

Terdapat dua class dalam kasus yaitu [Device.java](../tree/master/src/girish/modularization/broken/before/Device.java) dan [DeviceData.java](../tree/master/src/girish/modularization/broken/before/DeviceData.java). Pada kasus tersebut, member-member dari `Device` dipisahkan ke class lain yang bernama `DeviceData`, dimana method-method dari `Device` akan memanggil data-data dari class `DeviceData`. Hal ini seharusnya tidak boleh dilakukan dalam OOP dikarenakan class seharusnya menampung semua method dan member yang mempunyai tanggungjawab yang sama.

Kasus inilah yang pada akhirnya menimbulkan smell **Broken Modularization** karena adanya perpecahan modul yang memiliki tanggungjawab yang sama dari kelas seharusnya.

#### Penyelesaian

Untuk menyelesaikan kasus smell tersebut, gabungkan field dan method menjadi 1 class sehingga class Device dapat menampung method, member, dan field sesuai tanggungjawabnya masing-masing. Pada class [Device.java](../tree/master/src/girish/modularization/broken/after/Device.java), semua member pindahan dari `DeviceData` ditampung sebagai private member dari `Device`.

### When to Ignore

#### Auto-generated code
Jika code tersebut digenerate secara otomatis dari generator (dari higher-level models, terdiri atas beberapa data class), maka hal tersebut memang dapat diabaikan karena dapat menimbulkan efek *out of sync* jika user melakukan modifikasi pada auto-generated codes. Contoh kasusnya adalah [GUI Builder](https://en.wikipedia.org/wiki/Graphical_user_interface_builder) ataupun [LinQ database modeling](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/linq/)

#### Data Transfer Objects (DTOs)

![Struktur DTO](img/girish/modularization/broken-dto.png "Struktur Data Transfer Object menurut Martin Fowler")

Martin Fowler mendefinisikan [Data Transfer Objects](https://martinfowler.com/eaaCatalog/dataTransferObject.html) tersebut sebagai perantara data terhadap [Remote Facade](https://martinfowler.com/eaaCatalog/remoteFacade.html) (aksesor data). Jika class tersebut merupakan class yang difungsikan untuk parsing data dari/ke API website, dimana class tersebut hanya berisikan data field, setter-getter, parser (JSON/XML/AJAX ke Object), dan serializer tanpa method dan behaviour lainnya. Pada kasus tersebut, smell dapat diabaikan demi alasan mempermudah proses transfer data ke network.


## Insufficient Modularization

[Link Video](https://www.youtube.com/watch?v=eRAoks2udlk&list=PLG_Cu5FmqSk2KHT6lXngRvcOmOzuk4_ju) |
[Materi](../tree/master/src/girish/modularization/insufficient)

Smell ini terjadi karena adanya abstraksi yang terlalu besar dan harus dipecahkan (dekomposisi) ke beberapa abstraksi baru. Terdapat 2 jenis smell yaitu:

- **Bloated interface**: Abstraksi yang mempunyai banyak member dalam public interface. Smell ini disamaratakan dengan [Large Class](Bloaters#large-class).
- **Bloated implementation**: Abstraksi dengan jumlah method yang banyak ataupun memiliki kompleksitas yang terlalu kompleks. Smell ini disamaratakan dengan [Long Method](Bloaters#long-method).

### Teori: Cyclomatic Complexity

Pada kedua jenis smell ini, kompleksitas pada suatu method dapat diprediksi dengan [rumus cyclomatic complexity McCabe](https://en.wikipedia.org/wiki/Cyclomatic_complexity) (bukan McTerong) yaitu *`M = E − N + 2P`* dimana:

- *M* merepresentasikan magic number untuk kompleksitas cyclomatic per-satuan method/routines,
- *E* merepresentasikan jumlah edges (alur method),
- *N* merepresentasikan jumlah nodes (statements), dan
- *P* merepresentasikan jumlah connected components (jumlah method) yang dijalankan. (Tambahkan P jika dilakukan secara rekursif atau tambahkan 2 jika hanya 1 method)

Jika magic number cyclomatic complexity dari implementation (satuan method) tersebut **lebih dari 10**, maka smell Insuffient Modularization tipe bloated implementation bisa terjadi pada abstraksi tersebut.

Untuk mengetahui lebih jauh mengenai kompleksitas suatu method/abstraksi secara cyclomatic, dapat dilihat referensi lebih jauh melalui buku "Software Testing: A Craftsman's Approach, Third Edition" karangan Paul C. Jorgensen (Bab 9, Hal 139-146).

### Penyebab

- **Providing centralized control**: Dimana pada kasus smell ini disebabkan karena adanya pemusatan  kerjaan dan kendali dalam satu abstraksi atau dalam suatu method dalam abstraksi.
- **Creating large classes for use by multiple clients**: Developer membuat class besar agar dapat dipakai oleh multiple client.
- **Grouping all related functionality together**: Developer awam terkadang mengelompokkan beberapa fungsionalitas ke dalam 1 class tanpa memahami fungsinya Single Responsibility Principle (SRP) yang terkadang menimbulkan pembludakan interface/class.

### Penyelesaian

Jika kasus ini berkaitan dengan bloated interface, maka perlu dicek member (method dan field) beserta tanggungjawabnya. Jika member tersebut ternyata berperan diluar tanggungjawab utama class tersebut, extract ke class baru.

Jika kasus ini berkaitan dengan bloated implementation, perlu dicek kompleksitas method sebelum method tersebut dipecahkan/diekstrak ke private helper method. Jika kasus smell ini terjadi berbarengan dengan smell [Multifaceted Abstraction](Abstraction#multifaceted-abstraction), maka pecahkan/ekstrak dan enkapsulasikan setiap pertanggungjawaban dalam masing-masing abstraksi baru atau yang telah ada.

### Julukan

- [**God class**](https://en.wikipedia.org/wiki/God_object): Terjadi karena satu class terdiri dari > 50 method atau attributes.
- [**Fat interface**](https://en.wikipedia.org/wiki/Interface_bloat): Interface yang disediakan oleh class tidak kohesif.
- [**Blob class**](https://sourcemaking.com/antipatterns/the-blob): Class terlalu besar dan terlalu kompleks.
- **Classes with complex control flow**: Class terlalu kompleks secara cyclomatic complexity (nilai magic number of cyclomatic complexity sangat tinggi).
- **Too much responsibility**: Class memegang tanggungjawab terlalu banyak.

### When to Ignore

- **Key classes**: Merupakan class yang krusial dan penting, juga berukuran besar, kompleks, dan terhubung dengan banyak class.
- **Auto-generated code**: Jika code tersebut digenerate secara otomatis dari generator mempunyai method yang kompleks sehingga sulit untuk direfactor/modifikasi.


## Cyclically-dependent Modularization

[Link Video](https://www.youtube.com/watch?v=Xm5T75YZB0I&list=PLG_Cu5FmqSk2KHT6lXngRvcOmOzuk4_ju) |
[Materi](../tree/master/src/girish/modularization/cyclic)

![Graph of cyclic dependency](img/girish/modularization/cyclic-1.png "Graph of cyclic dependency")

Smell ini terjadi ketika dua atau lebih abstraction saling bergantung satu sama lain baik secara langsung maupun tidak langsung. Smell ini tentunya melanggar [Acyclic Dependencies Principle (ADP)](https://en.wikipedia.org/wiki/Acyclic_dependencies_principle).

Smell ini tidak hanya terjadi secara langsung secara inheritence dan field member, namun juga secara tidak langsung secara variabel dalam method maupun secara parametrik. Dampak dari smell ini adalah keharusan sang developer untuk menjalankan, tes, dan modifikasi pasangan cyclic class secara bersamaan, yang bahayanya dapat berpotensi terjadinya *domino-effect* ataupun [*shotgun-surgery*](Change-Preventers#shotgun-surgery) karena harus merubah class yang cyclic secara bersamaan atau dalam kasus fatalnya, bisa menjadi "senjata makan tuan" dari class pemanggil methodnya sendiri.

### Penyebab

- **Improper responsibility realization**: Adanya salah penempatan member abstraksi di abstraksi lain.
- **Passing a self reference**: Adanya passing dengan penggunaan `this` (reference dirinya) ketika melakukan passing referensi ke method dari abstraksi lain.
- **Implementing call-back functionality**: Adanya pemakaian call-back menimbulkan adanya dependensi cyclic antar class yang tidak diduga.
- **Hard-to-visualize indirect dependencies**: Dalam beberapa software kompleks, cukup sulit bagi developer untuk visualisasikan hubungan dependensi antar abstraksi sehingga menimbulkan perputaran dependensi antar abstraksi/class.

### Contoh

#### Contoh 1: `java.util` inter-abstractions cyclic interdepedency

![Java interclass cyclic dependency](img/girish/modularization/cyclic-2.png "Java Date, Calendar, and TimeZone interclass cyclic dependency")

Dalam buku yang ditulis oleh Girish, Girish menyinggung package `java.util` yang berisikan 6 abstractions yang sangat kompleks dan sangat cyclic ketergantungan antar classnya. Class tersebut hampir semuanya saling bergantung satu sama lain.

Secara mendasar, Girish juga menuliskan catatan mengenai adanya potensi *domino-effect* karena adanya perubahan antar class ketika terdapat perubahan dalam satu abstraksi yang secara eksplisit dikutipkan sebagai berikut (hal 108-109, Chapter 5):

> In this design, any change to an abstraction involved in this dependency chain has the potential to affect other abstractions that depend on it, causing **ripple effects or cascade of changes**. A designer must, therefore, strive for designs that do not consist of tangles.

Meski secara hirerarki cukup sulit dalam memberantas adanya smell pada tingkat tinggi, apalagi pada level programming language API, namun kasus ini bisa dicegah dari bibit-bibitnya pada contoh kasus berikutnya.

#### Contoh 2: Order and TaxCalculator

![Struktur class Order dan TaxCalculator](img/girish/modularization/cyclic-3.png "Cyclic dependency antara class Order dan TaxCalculator")

Pada kasus class [Order.java](../tree/master/src/girish/modularization/cyclic/before/Order.java), [TaxCalculator.java](../tree/master/src/girish/modularization/cyclic/before/TaxCalculator.java), dan [Item.java](../tree/master/src/girish/modularization/cyclic/before/Item.java), terdapat perputaran dependensi antar class. Member field class `Order` terdiri dari (composed of) beberapa `Item`, namun pada class `Order`, terdapat method `getAmount()` dimana dalam isi methodnya melakukan return `taxCal.computeAmount()`. Pada method `computeAmount()`, class tersebut mengambil semua item dalam class `Order` dan menghitung jumlah pajak dari transaksi order dengan memanggil method `calculateTax()` untuk kemudian ditotalkan dengan transaksi order tersebut dan di-return.

```java
// class Item
public double getCost() {
  return cost;
}

// class Order
public Vector<Item> getItems() {
  Vector<Item> items = new Vector<>();
  for (Item item : this.items) {
    items.add(new Item(item.getCost()));
  }
  return items;
}

public double getAmount() {
  TaxCalculator calc = new TaxCalculator();
  return calc.computeAmount(this);
}

// class TaxCalculator
public double computeAmount(Order order) {
  Vector<Item> items = order.getItems();

  double amount = 0;
  for (Item item : items) {
    amount += item.getCost();
  }

  return amount + calculateTax(amount);
}

private double calculateTax(double amount) {
  return amount * 0.1;
}
```

Secara tidak langsung, pemanggilan method `getAmount()` dari class `Order` secara tidak langsung menyebabkan adanya cyclic dependency yang menyebabkan ketergantungan yang berputar-putar. Selain itu, method dalam class ini dicurigai mempunyai smell lain yaitu [Feature Envy](Couplers#feature-envy) dimana method ini hanya mengakses value dari class lain dan secara multidimensional, smell ini terjadi bagaikan lingkaran setan dimana terjadi *adu colong sandal* antar class sehingga rawan terjadi error karena pasangan class tersebut harus dijalankan, dites, dan diubah bersamaan.

### Penyelesaian

Untuk mencegah terjadinya cyclic depedency, ada berbagai cara untuk menyelesaikan kejadian smell ini yaitu:

- Introduce Interface
- Breaking cyclic by removing a Dependency
- Introduce another abstraction
- Merging the abstractions

Pada kasus class `Order` dan `TaxCalculator`, diketahui bahwa terdapat method `computeAmount()` yang sebetulnya dapat ditempatkan di class `Order` sehingga class `TaxCalculator` cukup mengkalkulasikan pajak dari total transaksi keseluruhan.

```java
// class Item
public double getCost() {
  return cost;
}

// class Order
public Vector<Item> getItems() {
  Vector<Item> items = new Vector<>();
  for (Item item : this.items) {
    items.add(new Item(item.getCost()));
  }
  return items;
}

public double getAmount() {
  return computeAmount() + calc.calculateTax(amount);
}

private double computeAmount() {
  TaxCalculator calc = new TaxCalculator();
  Vector<Item> items = getItems();

  double amount = 0;
  for (Item item : items) {
    amount += item.getCost();
  }

  return amount;
}

// class TaxCalculator
private double calculateTax(double amount) {
  return amount * 0.1;
}
```

Sebagai gantinya, kita dapat memindahkan method `computeAmount()` dari class TaxCalculator ke Order sehingga class [TaxCalculator.java](../tree/master/src/girish/modularization/cyclic/after/Order.java) tidak perlu bergantung pada [Order.java](../tree/master/src/girish/modularization/cyclic/after/TaxCalculator.java) untuk mengambil data-data dari Order dan semua kalkulasi biaya cukup dilakukan dari class `Order` dan class `TaxCalculator` cukup mengkalkulasikan berbagai jenis pajak yang akan diaplikasikan kepada total biaya dari class `Order`.

### When to Ignore

Pada kasus **Unit cycles between conceptually related abstractions**, terutama pada kasus class `java.util.regex`, terdapat 2 class yang saling bertukar peran dan saling bergantung satu sama lain yaitu class `Matcher` dan `Pattern`. Dalam kasus tersebut, kedua class memang diciptakan terpisah namun terpakai berbarengan. Meski praktik ini tepat untuk diterapkan, namun dalam beberapa kasus tertentu, terutama pada skala besar, class tetap harus dibagi untuk mencegah adanya masalah yang lebih kompleks.


## Hub-like Dependencies

[Link Video](https://www.youtube.com/watch?v=ImUM8T-1fy4&list=PLG_Cu5FmqSk2KHT6lXngRvcOmOzuk4_ju) |
[Materi](../tree/master/src/girish/modularization/hub)

![Inner and outer dependencies of a class](img/girish/modularization/hub-1.png "Inner and outer dependencies of a class")

Smell ini terjadi ketika sebuah abstraksi mempunyai banyak dependensi masukan (fan-in) dan/atau abstraksi tersebut bergantung banyak pada abstraksi lain (fan-out). Smell ini sering kali memiliki kombinasi dengan smell lain seperti Large Class/Insufficient Modularization, Divergent Changes, dan Broken Modularization. Smell ini tentunya melanggar Single Responsibility Principle (SRP) karena banyaknya tanggungjawab yang dibebani olehnya.

### Penyebab

- **Improper responsibility assignment (to hub class)**: Dikarenakan abstraksi tersebut banyaknya tanggungjawab dan kadangkala harus berkomunikasi dengan class lain.
- **“Dump-all” utility classes**: Developer membuat sebuah class *Helper* agar class lain dapat memanfaatkan utility yang diperlukan dari *Helper* class tersebut.

### Contoh

#### Kasus class `java.awt.Font` dan `java.util.Component`

Pada buku ini, disebutkan bahwa Girish menyinggung 2 class besar yang mempunyai smell ini, yaitu class `java.awt.Font` dan `java.util.Component`.

Pada kasus smell class `java.awt.Font`, class ini merupakan class yang merepresentasikan font dan fungsionalitas untuk render text sebagai kumpulan abjad/karakter pada object `Graphic` dan `Component`. Smell ini terjadi karena terhitung ada 19 incoming dependencies (fan-in) dan 34 outgoing dependencies (fan-out). Tentunya jumlah dependensi dalam class ini terbilang cukup banyak karena adanya text drawing dalam komponen-komponen di Java.

Pada kasus smell class `java.util.Component`, class ini berisikan konsep object grafis yang dapat ditampilkan pada layar, baik sebagai button, panel, dan scroll bar. Secara definitif, class ini merupakan fondasi penting dalam pengembangan user interface dalam bahasa pemrograman Java. Smell ini terjadi karena terhitung ada 498 incoming dependencies (fan-in termasuk `java.awt.Font`) dan 71 outgoing dependencies (fan-out).

Kedua class besar ini juga belum dihitung jumlah dependensi secara implisit (method implementasi dalam class), dan tentunya memperbludak class management karena banyaknya dependensi yang harus diurus oleh class tersebut.

#### Kasus utility class

Diketahui sebuah class bernama [Utility.java](../tree/master/src/girish/modularization/hub/before/Utility.java) dimana pada class tersebut berisikan berbagai macam jenis utility yang dapat dipergunakan oleh class lain.

```java
public class Utilities {
  public static boolean validateEmail(String email) { ... }
  public static boolean validatePhoneNumber(String phoneNumber) { ... }

  //... other validation methods

  public static String toTitleCase(String s) { ... }

  //... other string manipulation methods
}
```

Ternyata class tersebut tidak hanya bertanggungjawab atas validasi, namun juga pada manipulasi teks dan fitur-fitur pembantu lainnya sehingga menimbulkan smell hub-like modularization dan Large Class karena adanya pemakaian oleh beberapa class lainnya.

### Penyelesaian

- Jika class tersebut mempunyai tanggungjawab yang banyak, disarankan untuk memecahkan menjadi beberapa class dengan [Extract Class](https://refactoring.guru/extract-class).
- Jika dependensi class disebabkan karena salah tempat, pindahkan member tersebut ke class yang bersangkutan.
- Gunakan design pattern yang tepat untuk mengurangi jumlah dependensi class, salah satunya adalah [Chain of Responsibility](https://refactoring.guru/design-patterns/chain-of-responsibility) dimana class tersebut berfungsi untuk melakukan pemanggilan berantai antar class atau alternatifnya, menggunakan design pattern [Observer](https://refactoring.guru/design-patterns/observer) dimana class tersebut berfungsi untuk melakukan panggilan kepada class yang diaggregatnya.

#### Penyelesaian pada kasus `java.awt.Font`

Salah satu sugesti dari Girish adalah dengan memindahkan class, dikarenakan adanya beberapa class yang mempunyai member (baik method dan attribute) yang dapat tempatkan pada class yang seharusnya, salah satunya adalah method `createGlyphVector()` yang mempunyai smell Feature Envy dimana method ini berfungsi hanya untuk mengakses class `GlyphVector` dan membuat object dari class tersebut.

```java
public GlyphVector createGlyphVector(FontRenderContext frc. CharacterIterator ci) {
  return (GlyphVector) new StandardGlyphVector(this, ci, frc);
}
```

Salah satu saran refactoring adalah dengan memindahkannya ke class `GlyphVector` sehingga class yang bergantung pada method tersebut dapat mengakses langsung pada class `GlyphVector`.

#### Penyelesaian pada kasus utility class

```java
public class Utilities {
  public static String toTitleCase(String s) { ... }
  public static boolean validatePhoneNumber(String phoneNumber) { ... }

  //... other string manipulation methods
}

public class Validation {
  public static boolean validateEmail(String email) { ... }

  //... other validation methods
}
```

Class `Utility` memegang tanggung jawab lebih dari 1 yaitu validasi dan string manipulation, oleh karenanya diperlukan extract class ke class baru bernama [Validation.java](../tree/master/src/girish/modularization/hub/after/Validation.java) untuk keperluan sehingga pada class [Utility.java](../tree/master/src/girish/modularization/hub/after/Utility.java) cukup dipakai untuk keperluan string manipulation.

### Julukan

- **Bottlenecks**: Smell ini terjadi jika ada class yang banyak dipakai kepada class lain.
- **Local hubs**: Smell ini terjadi ketika sebuah abstraksi punya banyak dependensi dan dipakai oleh banyak class.
- **Man-in-the-middle**: Smell ini terjadi ketika terdapat central class dalam hirerarki abstraksi yang sering dimanfaatkan sebagai mediator oleh class lain.

### When to Ignore

Pada kasus-kasus aplikasi berskala besar, terkadang ada beberapa *core abstractions* dimana class tersebut berperan penting dalam aplikasi. Salah satu contoh class bertipekan core abstraction adalah `java.lang.Class` dimana class ini menjadi pusat abstraksi di JDK dimana class ini dipakai oleh >1000 class dan bergantung pada 40 class. Meski class ini dapat dicap mempunyai smell Hub-like Modularization smell, namun kasus ini sangat tidak memungkinkan untuk dibatasi jumlahnya karena fungsionalitas yang sangat penting bagi class lain.

---

### Referensi Bacaan

Wiki dan repository ini hanyalah rangkuman dari buku Suryanarayana et al. dengan sedikit tambahan informasi lain. Diharapkan mahasiswa juga membaca sumber aslinya pada bab 5 halaman 92 - 122.

Untuk referensi tambahan mengenai materi modularization, terutama pada topik tertentu, dapat disimak pada referensi berikut:

- Paul C. Jorgensen, "Software Testing: A Craftsman's Approach, Third Edition", Bab 9, Hal 139-146. CRC Press. 2013.
- Brian Mearns, "Circular Dependencies in Dependency Injection," 11 Apr 2018. Medium. Available at https://medium.com/software-ascending/circular-dependencies-in-dependency-injection-403b790daebb
