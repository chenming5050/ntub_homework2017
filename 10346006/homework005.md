#宣告物件
class Customer
  #設定屬性
  attr_reader :name  #名字

  def initialize(name) #初始化
    @name    = name #實體變數
    @rentals = [] #實體變數
  end

  def add_rental(arg) #增加Customer借的電影
    @rentals << arg
  end

  def statement ##定義一個statement方法
    total_amount = 0   #總共花費
    frequent_renter_points = 0  #紅利
    result = "Rental Record for #{@name}\n"  #印出租借電影名稱

    @rentals.each do |element|
      this_amount = 0
      case element.movie.price_code  #判斷級別
      when Movie::REGULAR #如果是普遍級
        this_amount += 2 #價格+2
        this_amount += (element.days_rented - 2) * 1.5 if element.days_rented > 2 #超過2天以上沒還再加錢
      when Movie::NEW_RELEASE #如果是新上市
        this_amount += element.days_rented * 3 #租的天數*3
      when Movie::CHILDRENS #如果是兒童級
        this_amount += 1.5
        this_amount += (element.days_rented - 3) * 1.5 if element.days_rented > 3 #超過3天以上沒還再加錢
      end
      #紅利點數計算
      frequent_renter_points += 1  #有借就給你1點
      if element.movie.price_code == Movie::NEW_RELEASE && element.days_rented > 1
        frequent_renter_points += 1 #如果是新上市又借超過1天以上在給你1點
      end

      result += "\t" + element.movie.title + "\t" + this_amount.to_s + "\n" #印出名稱跟這片價格
      total_amount += this_amount #總金額加上這部片金額
    end

    result += "Amount owed is #{total_amount}\n"  #amount總共價格
    result += "You earned #{frequent_renter_points} frequent renter points" #你賺了多少紅利點數
    result
  end
end

####
#宣告物件
class Movie
  #影片種類
  REGULAR     = 0   #普遍級
  NEW_RELEASE = 1   #新上市
  CHILDRENS   = 2   #兒童級

  attr_reader :title #影片名稱
  attr_accessor :price_code #價格

  def initialize(title, price_code)  #初始化
    @title, @price_code = title, price_code
  end
end

####
#宣告物件
class Rental
  #設定屬性
  attr_reader :movie, :days_rented

  def initialize(movie, days_rented) #初始化
    @movie, @days_rented = movie, days_rented
  end
end

####

client = Customer.new('eddie') #新增一個'eddit'用戶

movie1 = Movie.new('ruby', Movie::NEW_RELEASE)  #以Movie這個類別，新增一部電影'ruby'，級別為NEW_RELEASE
rental1 = Rental.new(movie1, 3)    #以Rental這個類別，新增'movie1'期限7天
client.add_rental rental1   #eddit 借 ruby這部電影

movie2 = Movie.new('php', Movie::REGULAR)  #以Movie這個類別，新增一部電影'php'，級別為REGULAR
rental2 = Rental.new(movie2, 7) #以Rental這個類別，新增'movie2'期限7天
client.add_rental rental2 #eddit 借 ruby這部電影

puts client.statement #列出Customer的租借狀態

#最後puts結果為 Rental Record for eddie ruby 9 php 9.5 Amount owed is 18.5 You earned 3 frequent renter points =end


/*流程
這是一個電影租借系統
判斷級別
如果是REGULAR 價格是2 如果超過3天就加1.5
如果是NEW_RELEASE 價格是租的天數*3
如果是CHILDRENS 價格是1.5 如果超過3天以上再加1.5

點數計算有借就給你1點
如果是新上市又借超過1天以上在給你1點

最後印出所有你買了哪部電影價格多少，總共價格是多少，你賺了多少紅利點數
/
