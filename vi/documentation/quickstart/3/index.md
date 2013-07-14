---
layout: page
title: "Tìm hiểu Ruby trong 25 phút"
lang: vi

header: |
  <div class="multi-page">
    <a href="../" title="Phần 1">1</a>
    <span class="separator"> | </span>
    <a href="../2/" title="Phần 2">2</a>
    <span class="separator"> | </span>
    <strong>3</strong>
    <span class="separator"> | </span>
    <a href="../4/" title="Phần 4">4</a>
  </div>
  <h1>Tìm hiểu Ruby trong 25 phút</h1>

---

Bây giờ, chúng ta hãy tạo một đối tượng Greeter và sử dụng nó:

{% highlight ruby %}
irb(main):035:0> g = Greeter.new("Pat")
=> #<Greeter:0x16cac @ten="Pat">
irb(main):036:0> g.xin_chao
Xin chào Pat!
=> nil
irb(main):037:0> g.tam_biet
Tạm biệt Pat, hẹn gặp lại.
=> nil
{% endhighlight %}

Khi đối tượng `g` được tạo, nó nhớ rằng cái tên là Pat. Vậy nếu chúng ta muốn lấy một cái tên trực tiếp thì sao?

{% highlight ruby %}
irb(main):038:0> g.@ten
SyntaxError: compile error
(irb):52: syntax error
        from (irb):52
{% endhighlight %}

Ồ, không thể làm được.

## Bên trong của đối tượng

Các biến ví dụ thường tồn tại ẩn trong trong đối tượng. Chúng không ẩn hoàn toàn, bạn sẽ thấy chúng mỗi khi khởi tạo một đối tượng và có nhiều cách để gọi chúng, nhưng Ruby sử dụng phương pháp tiếp cận hướng đối tượng tốt trong việc giữ dữ liệu ẩn đi phần nào.

Vậy những phương thức nào tồn tại cho đối tượng Greeter?

{% highlight ruby %}
irb(main):039:0> Greeter.instance_methods
=> ["method", "send", "object_id", "singleton_methods",
    "__send__", "equal?", "taint", "frozen?",
    "instance_variable_get", "kind_of?", "to_a",
    "instance_eval", "type", "protected_methods", "extend",
    "eql?", "display", "instance_variable_set", "hash",
    "is_a?", "to_s", "class", "tainted?", "private_methods",
    "untaint", "xin_chao", "id", "inspect", "==", "===",
    "clone", "public_methods", "respond_to?", "freeze",
    "tam_biet", "__id__", "=~", "methods", "nil?", "dup",
    "instance_variables", "instance_of?"]
{% endhighlight %}

Quá tuyệt! Có rất nhiều phương thức. Chúng ta chỉ định nghĩa 2 phương thức, vậy cái gì đây? Đó là **tất cả* các phương thức cho đối tượng Người chào đón, một danh sách hoàn chỉnh bao gồm cả những phương thức được kế thừa. Nếu chúng ta muốn một danh sách chỉ có các phương thức định nghĩa cho Greeter, chúng ta có thể loại bỏ các phương thức được kế thừa bằng cách thêm tham số `false`, nghĩa là chúng ta không muốn bất kỳ phương thức được kế thừa nào.

{% highlight ruby %}
irb(main):040:0> Greeter.instance_methods(false)
=> ["xin_chao", "tam_biet"]
{% endhighlight %}

A, được hơn rồi đó. Bây giờ chúng ta hãy xem phương thức nào được đối tượng Greeter phản hồi:

{% highlight ruby %}
irb(main):041:0> g.respond_to?("ten")
=> false
irb(main):042:0> g.respond_to?("xin_chao")
=> true
irb(main):043:0> g.respond_to?("to_s")
=> true
{% endhighlight %}

Nó hiểu được `xin_chao` và `to_s` (to string - chuyển sang xâu, một phương thức mặc định cho mọi đối tượng), nhưng không biết `ten`.
So, it knows `xin_chao`, and `to_s` (meaning convert something to a
string, a method that’s defined by default for every object), but it
doesn’t know `name`.

## Thay thế các lớp - Không bao giờ là quá muộn

Nếu bạn muốn có thể xem hoặc thay đổi tên? Ruby cung cấp một cách dễ dàng để truy xuất dữ liệu tới các biến của một đối tượng.

{% highlight ruby %}
irb(main):044:0> class Greeter
irb(main):045:1>   attr_accessor :name
irb(main):046:1> end
=> nil
{% endhighlight %}

Trong Ruby, bạn có thể mở một lớp ở phía trên và sửa nó. Sự thay đổi sẽ xuất hiện trong các đối tượng mới mà bạn tạo và cả những đối tượng đã tồn tại của lớp này. Vậy thì chúng ta hãy tạo một đối tượng mới và thử với thuộc tính `@ten` của nó.

{% highlight ruby %}
irb(main):047:0> g = Greeter.new("Andy")
=> #<Greeter:0x3c9b0 @ten="Andy">
irb(main):048:0> g.respond_to?("ten")
=> true
irb(main):049:0> g.respond_to?("ten=")
=> true
irb(main):050:0> g.xin_chao
Xin chào Andy!
=> nil
irb(main):051:0> g.ten="Betty"
=> "Betty"
irb(main):052:0> g
=> #<Greeter:0x3c9b0 @ten="Betty">
irb(main):053:0> g.ten
=> "Betty"
irb(main):054:0> g.xin_chao
Xin chào Betty!
=> nil
{% endhighlight %}

Sử dụng `attr_accessor` định nghĩa 2 phương thức mới cho chúng ta: `ten` để lấy giá trị và `ten=` để gán giá trị.

## MegaGreeter chào mừng tất cả mọi thứ

Greeter này không phải là tất cả sự thú vị, mặc dù nó chỉ có thể giao tiếp với một người một lúc. Sẽ thế nào nếu chúng ta có thể vài kiểu MegaGreeter có thể đồng thời chào Thế giới, chào một người nào đó hay một danh sách cá nhân?

Chúng ta sẽ viết trong một tập tin thay vì gõ trực tiếp trên IRB.

Để thoát IRB, gõ "quit", "exit" hoặc tổ hợp phím Control-D.

{% highlight ruby %}
#!/usr/bin/env ruby

class MegaGreeter
  attr_accessor :names

  # Create the object
  def initialize(names = "Thế giới")
    @names = names
  end

  # Nói xin chào tới mọi người
  def xin_chao
    if @names.nil?
      puts "..."
    elsif @names.respond_to?("each")
      # @names là danh sách tên, lặp đi lặp lại!
      @names.each do |name|
        puts "Xin chào #{name}!"
      end
    else
      puts "Xin chào #{@names}!"
    end
  end

  # Tạm biệt mọi người
  def tam_biet
    if @names.nil?
      puts "..."
    elsif @names.respond_to?("join")
      # Gộp các thành phần của danh sách bằng dấu phẩy
      puts "Tạm biệt #{@names.join(", ")}.  Hẹn gặp lại!"
    else
      puts "Tạm biệt #{@names}.  Hẹn gặp lại!"
    end
  end

end


if __FILE__ == $0
  mg = MegaGreeter.new
  mg.xin_chao
  mg.tam_biet

  # Đổi tên thành "Zeke"
  mg.names = "Zeke"
  mg.xin_chao
  mg.tam_biet

  # Đổi tên thành danh sách tên
  mg.names = ["Albert", "Brenda", "Charles",
    "Dave", "Engelbert"]
  mg.xin_chao
  mg.tam_biet

  # Đổi thành rỗng
  mg.names = nil
  mg.xin_chao
  mg.tam_biet
end
{% endhighlight %}

Lưu tập tin này với tên “ri20min.rb”, và chạy nó “ruby ri20min.rb”. Màn hình sẽ hiện như này:

    Xin chào Thế giới!
    Tạm biệt Thế giới.  Hẹn gặp lại!
    Xin chào Zeke!
    Tạm biệt Zeke.  Hẹn gặp lại!
    Xin chào Albert!
    Xin chào Brenda!
    Xin chào Charles!
    Xin chào Dave!
    Xin chào Engelbert!
    Tạm biệt Albert, Brenda, Charles, Dave, Engelbert.  Come
    back soon!
    ...
    ...
{: .code}

Có rất nhiều thứ mới trong ví dụ cuối cùng này và chúng ta [có thể có cái nhìn sâu hơn tại.](../4/)

