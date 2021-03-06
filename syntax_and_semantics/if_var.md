# Değişkenli(Var) if

Eğer bir değişken bir `if`'in koşulu ise, `then` bölümünün içinde `Nil` tipi
yokmuş gibi hesaba katılır.

```crystal
a = bir_koşul ? nil : 3
# a Int32 veya Nil

if a
  # Eğer a doğruluk ise bu bölüme geçebilir,
  # a nil olamaz. Böylece burada a Int32'dir.
  a.abs
end
```

Bu, bir `if` ifadesinin koşulunda bir değişken atandığında da geçerlidir:

```crystal
if a = bir_ifade
  # a burada nil değildir.
end
```

Bu mantık, koşulda ve(`&&`) varsa da geçerlidir:

```crystal
if a && b
  # bu bölümde hem a hem b için Nil olamayacakları kesindir.
end
```

Burada, `&&` ifadesinin sağ tarafında da `a`'nın 'Nil` olamayacağı kesindir.

Bir değişkeni `then` bölümünün içinde tekrardan atamak, o değişkeni tabiki de atanan ifadenin tipine bağlı olarak değiştirecektir.

Yukarıdaki mantık örnek ve sınıf değişkenleri için **çalışmaz** ve geçerli değildir:

```crystal
if @a
  # burada @a nil olabilir
end
```

Çünkü herhangi bir metot çağrısı, olanak dahilinde o örnek değişkeni etkileyebilir ve `nil` yapabilir. Başka bir nedenle, koşulun denetlenmesinden sonra ayrı bir thread o örnek değişkenin değerini değiştirebilir.

`@a` ile `nil` olmadığı koşullar harici bir şey yapabilmek için iki seçenek bulunmaktadır:

```crystal
# Birincisi: başka bir değişkene atamak
if a = @a
  # burada a nil olamaz
end

# İkincisi: standart kütüphanede bulunan `Object#try` kullanmak
@a.try do |a|
  # burada a nil olamaz
end
```

Bu mantık, getiriciler(getter) ve özellikler(property) de dahil olmak üzere proc ve metot çağrılarıyla çalışmaz. Çünkü nil olabilen (veya daha genel olarak, bir birleşim tipinde olan) proclar ve metodların, iki ardışık çağrıdan aynı ve daha spesifik tipi döndürmesi garanti edilemez.

```crystal
if metot # ilk olarak Int32 or Nil dönebilen metodu çağır
         # burada biliyoruz ki ilk çağrı Nil dönmedi
  metot  # ikinci çağrı Int32 ya da Nil dönebilir
end
```

Yukarıda örnek değişkenler için açıklanan teknikler proc ve metot çağrıları için de çalışacaktır.

