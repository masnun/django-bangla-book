# টিউটোরিয়াল - তৃতীয় অধ্যায় 

### ভিউ - ব্যাসিক 

আমরা আগের আলোচনায় দেখেছি জ্যাঙ্গো একটি MVT (Model View Template) ফ্রেমওয়ার্ক । এই পর্বে আমরা ভিউ নিয়ে কাজ করবো । জ্যাঙ্গোতে একেকটি ভিউ হলো একেকটি পেইজের মত । প্রত্যেকটি ভিউ একটি নির্দিষ্ট কাজ করে এবং সাধারণত কাজ শেষে একটি টেম্প্লেট রেন্ডার করে । 

যেমন আমরা যদি আমাদের অনলাইন পোল এ্যাপটির কথাই চিন্তা করি তবে আমাদের কতগুলো ভিউ এর দরকার পড়বে - 

* একটি পেইজে থাকবে সবগুলো পোল এর তালিকা । যেখান থেকে লিংক এ ক্লিক করে আমি যে কোন একটি নির্বাচন করতে পারবো । 
* একটি পেইজে থাকবে নির্বাচিত পোল এর ডিটেইলস । এখানে থাকবে প্রশ্ন এবং ভোট করার জন্য অপশনগুলো । 
* রেজাল্ট পেইজ থাকবে যেখানে একটি পোলের রেজাল্ট প্রদর্শন করা হবে 
* আরেকটি পেইজ ব্যবহারকারীর সাবমিট করা ভোটটি প্রসেস করবে । প্রসেসিং শেষে অন্য কোথাও রিডিরেক্ট করে দিবে । 


### আমদের প্রথম ভিউ 

তাহলে এবার আমাদের প্রথম ভিউ লিখে দেখা যাক বাস্তবে এটা কিভাবে কাজ করে । ঝটপট `polls\views.py` খুলে চটপট করে লিখে ফেলুন নিচের কোডটুকু - 

	from django.http import HttpResponse

	def index(request):
    	return HttpResponse("Hello, world. You're at the poll index.")


ব্যাস, আমাদের ভিউ তৈরি করা শেষ । এবার পালা এই ভিউটিকে একটি URL এর সাথে সংযুক্ত করার । এজন্য আমরা একটি `URLconf` তৈরি করবো । `URLconf` নামটি জটিল হলেও এর কাজ কিন্তু বেশ সহজ সরল । কোড দেখলেই বুঝতে পারবো । আমরা `urls.py` নামে একটি ফাইল তৈরি করবো `polls` ডিরেক্টরীর মধ্য এবং এই কোড যোগ করবো - 


	from django.conf.urls import patterns, url

	from polls import views

	urlpatterns = patterns('',
    	url(r'^$', views.index, name='index')
	)

আসলে `URLconf` হলো `URL configuration` যেখানে আমরা বলে দেই কোন URL এর জন্য কোন ভিউ ব্যবহার করতে হবে । ফিক্সড URL এর পরিবর্তে একটি বিশেষ প্যাটার্ন এর URL বোঝানোর জন্য রেগুলার এক্সপ্রেশন ব্যবহার করারও সুযোগ রয়েছে । 

আমরা আমাদের নিজেদের এ্যাপ্লিকেশন এর জন্য `urls.py` ফাইলে URLconf যোগ করলাম । এবার পালা এটাকে মূল URLconf এর সাথে যোগ করার । আমরা দেখেছিলাম `mysite/urls.py` ফাইলে প্রজেক্ট এর সব URLconf ছিলো । সেখানে আমরা এ্যাডমিন এ্যাপ্লিকেশন এর জন্য URLconf ইম্পোর্ট করেছিলাম । এবার আমরা আমাদের এ্যাপ্লিকেশনের জন্যও URLconf ইম্পোর্ট করবো যাতে জ্যাঙ্গো রান করার সময় আমাদের URLconf লোড করে নেয় । আর সেটি করলে প্রকারান্তরে জ্যাঙ্গো জানবে ঠিক কোন URL এর জন্য আমাদের এ্যাপ্লিকেশন এর কোন ভিউটি রান করতে হবে । 

এবার তাহলে `mysite/urls.py` ফাইলটি খুলে নিচের কোড যোগ করে দেই - 

	from django.conf.urls import patterns, include, url

	from django.contrib import admin
	admin.autodiscover()

	urlpatterns = patterns('',
    	url(r'^polls/', include('polls.urls')),
    	url(r'^admin/', include(admin.site.urls)),
	)
	
এখানে আমরা জ্যাঙ্গোকে জানিয়ে দিচ্ছি URL এর শুরুতে `polls` থাকলে `polls.urls` মডিউল থেকে URLconf লোড করে নিতে । 

### যোগ করি আরো কিছু ভিউ 

এতক্ষণে আমরা কিভাবে ভিউ যোগ করতে হয় তা দেখলাম । এবার আমাদের এ্যাপ্লিকেশন এর জন্য প্রয়োজনীয় আরো কয়েকটি ভিউ যোগ করে নেই । বরাবরের মত `polls/views.py` ফাইলটি খুলে নিচের লাইনগুলো যোগ করে দেই - 

	def detail(request, poll_id):
    	return HttpResponse("You're looking at poll %s." % poll_id)

	def results(request, poll_id):
    	return HttpResponse("You're looking at the results of poll %s." % poll_id)

	def vote(request, poll_id):
    	return HttpResponse("You're voting on poll %s." % poll_id)
    	
 এখানে আমরা ৩টি নতুন ভিউ যোগ করলাম - detail, results, vote - এবার এগুলোকে আমরা URLconf এ যোগ করবো । `polls/urls.py` ফাইলে প্রয়োজনীয় পরিবর্তন করে নেই যেন ফাইলটির কনটেন্ট নিম্নরূপ হয় - 
 
 
	from django.conf.urls import patterns, url

	from polls import views

	urlpatterns = patterns('',
    	# ex: /polls/
    	url(r'^$', views.index, name='index'),
    	# ex: /polls/5/
    	url(r'^(?P<poll_id>\d+)/$', views.detail, name='detail'),
    	# ex: /polls/5/results/
    	url(r'^(?P<poll_id>\d+)/results/$', views.results, name='results'),
    	# ex: /polls/5/vote/
    	url(r'^(?P<poll_id>\d+)/vote/$', views.vote, name='vote'),
	)
	
	
এবার আপনার ব্রাউজারে ভিজিট করুন - `http://localhost:8000/polls/34` - দেখবেন `detail()` ভিউ রান করেছে । এমনি করে `http://localhost:8000/polls/34/results/` কিংবা `http://localhost:8000/polls/34/vote/` এ্যাড্রেস গুলো ভিজিট করে দেখুন । যথাক্রমে `results()` এবং `vote()` ভিউ দুটি থেকে আউটপুট পাবো আমরা । 

এখানে উল্লেখ্য আমরা URL প্যাটার্ন নির্দেশ করার জন্য রেগুলার এক্সপ্রেশন ব্যবহার করেছি । রেগুলার এক্সপ্রেশান সম্পর্কে আরো বিস্তারিত জানতে চাইলে ঘুরে আসতে পারেন উইকিপিডিয়া থেকে - <a href="http://en.wikipedia.org/wiki/Regular_expression">http://en.wikipedia.org/wiki/Regular_expression</a> । সহজ কথায় ব্যখ্যা করতে গেলে `'^(?P<poll_id>\d+)/$'` এই অংশটুকু নির্দেশ করে `/polls/` এর পর যদি কোন সংখ্যা থাকে URL এ তবে যেন সেটির জন্য `detail()` ভিউটি ব্যবহার করা হয় । হয়তো ভাবছেন, রেগুলার এক্সপ্রেশনে তো `polls` এর কোন নাম বা চিহ্নও নাই তবে `/polls/` কোথা হতে আসলো? এটা আসলে এসেছে আমাদের মূল URLconf (যেটি কিনা `mysite/urls.py` ফাইলে আছে) হতে । ওখানে আমরা URL এর শুরুতে `/polls/` ম্যাচ করেছি । বাকিটা ম্যাচ করা হয়েছে এখানে । 

### কার্যকর ভিউ তৈরি করা  

ভিউ নিয়ে অনেক কিছুই দেখলাম এতক্ষন । এবার আসুন ভিউগুলোকে কাজের উপযোগী করে নেই । আগের ভিউগুলো আসলে কিছু করছিলো না । শুধু এক লাইন টেক্সট আউটপুট করছিলো । এবার আমরা এগুলোকে কাজের কাজী বানাবো । শুরু করি `index()` ভিউ দিয়ে । নিচের মত করে কোড লিখে ফেলি - 

	from django.http import HttpResponse

	from polls.models import Poll

	def index(request):
    	latest_poll_list = Poll.objects.order_by('-pub_date')[:5]
    	output = ', '.join([p.question for p in latest_poll_list])
    	return HttpResponse(output)
    	
ভিউগুলো আসলে একেকটি ফাংশন যা হয় `HttpResponse` অথবা কোনো এক্সেপশান (যেমন: `Http404`) রিটার্ন করবে । ভিউ থেকে রিটার্ন করা রেসপন্স জ্যাঙ্গো সরাসরি ব্রাউজারে আউটপুট করে । 

এখন সমস্যা হলো আমাদের আউটপুট হার্ডকোড করা । অর্থাৎ আউটপুট পরিবর্তন করতে হলে আমাদেরকে ভিউ এর পাইথন কোড পরিবর্তন করার প্রয়োজন পড়বে । এই সমস্যার সমাধান করতে পারে টেম্প্লেটস! 

### টেম্প্লেটস - বেসিক 

আসলে টেম্প্লেটস কি জিনিস? মা খালারা পিঠা বানানোর সময় এক ধরণের ছাচ ব্যবহার করেন । এর ভিতরে নকশা করাই থাকে । এই ছাচে পিঠা তৈরির উপকরণ বসিয়ে দিয়ে একটু কসরত করলেই পিঠার গায়ে তৈরি হয় নানা প্রকারের নকশা । টেম্প্লেট ও অনেকটা সেরকম । টেম্প্লেট এর কন্টেন্টগুলো বেশীরভাগই হয় স্ট্যাটিক যা পরিবর্তন হয় না । আর কিছু ডাইনামিক পার্ট থাকে যেগুলো প্রোগ্রামাবল । যখন টেম্প্লেট লোড করা হয় তখন টেম্প্লেট ইন্জিনকে এই ডাইনামিক পার্টগুলোর জন্য তথ্য সরবারহ করা হয় । টেম্প্লেট ইন্জিন সেই তথ্যের উপর ভিত্তি করে পুরো কন্টেন্ট রেন্ডার করে । 

যেমন ধরুন, একটা পেইজে আমাদের ব্যবহারকারীর নাম সহ একটা মেসেজ দেখানো প্রয়োজন । সেক্ষেত্রে নামটাই বারবার পরিবর্তন হবে, বাকি মেসেজটা একই থাকছে । আমরা কাজটি খুব সহজেই টেম্প্লেট দিয়ে করতে পারি । টেম্প্লেটটি হতে পারে এমন - 

	Hello {{ username }}, thank you for visiting! 
	
এখানে `{{ username }}` এই অংশটি ডাইনামিক । এবার ধরুন একজন ব্যবহারকারীর নাম 'masnun', আমরা টেম্প্লেটকে বলে দিবো username এর ভ্যালু হবে 'masnun', তখন আমরা আউটপুট পাবো এরকম - 

	Hello masnun, thank you for visiting! 


টেম্প্লেট ব্যবহার করলে আমাদেরকে পাইথন কোড নিয়ে ঘাটতে হচ্ছে না । ব্যাকেন্ড এর কোড আর ফ্রন্টএন্ড এর ডিজাইন খুব সহজেই পৃথক রাখতে পারছি । সেই সাথে টেম্প্লেট ইনহেরিট্যান্স, ব্লক প্রভৃতির সুবিধা নিয়ে আমরা খুব সহজে কোড মেইনটেইন করতে পারি । 

### জ্যাঙ্গোয় টেম্প্লেট 

জ্যাঙ্গোতে টেম্প্লেটস থাকে এ্যাপ্লিকেশনের `templates` ডিরেক্টরীতে । আমরা `polls` ডিরেক্টরীতে `templates` নামে একটি ফোল্ডার তৈরি করি । এই ডিরেক্টরীতে আবার `polls` নামে আরেকটি ডিরেক্টরী তৈরি করি আমাদের টেম্প্লেটগুলোর জন্য । এখানে আমরা `index.html` নামে একটি ফাইল তৈরি করি । ফুল পাথ হবে - `polls/templates/polls/index.html` । কন্টেন্ট হবে এরকম - 

	{% if latest_poll_list %}
    	<ul>
    	{% for poll in latest_poll_list %}
        	<li><a href="/polls/{{ poll.id }}/">{{ poll.question }}</a></li>
    	{% endfor %}
    	</ul>
	{% else %}
    	<p>No polls are available.</p>
	{% endif %}


এখানে `{{ poll.id }}` এবং `{{ poll.question }}` হলো ডাইনামিক কন্টেন্ট । এই টেম্প্লেটটির জন্য ভিউ টি মডিফাই করে নেই - 

	from django.http import HttpResponse
	from django.template import Context, loader

	from polls.models import Poll

	def index(request):
    	latest_poll_list = Poll.objects.order_by('-pub_date')[:5]
    	template = loader.get_template('polls/index.html')
    	context = Context({
        	'latest_poll_list': latest_poll_list,
    	})
    	return HttpResponse(template.render(context))
    	
 এবার আমরা ব্রাউজারে আউটপুট দেখে নেই । 
 
 এই উদাহরণে টেম্প্লেট রেন্ডার করার জন্য বেশ খানিকটা কোড লিখতে হয়েছে । এই কাজটা আমরা সহজে করতে পারি জ্যাঙ্গোর একটা শর্টকাট ব্যবহার করে । 
 
	from django.shortcuts import render

	from polls.models import Poll

	def index(request):
    	latest_poll_list = Poll.objects.all().order_by('-pub_date')[:5]
    	context = {'latest_poll_list': latest_poll_list}
    	return render(request, 'polls/index.html', context)
 
 
 `render()` হচ্ছে আলোচ্য শর্টকাট । এরকম অনেক শর্টকাট আছে যেগুলো আমাদের নিত্য নৈমিত্তিক কাজগুলোকে সহজ করে দেয় । 
 
### 404 ইরর পেইজ 

ওয়েবের খুব কমন একটা http error হলো 404 । এটির মানে কন্টেন্ট এ্যাভেইলেবল না । এই ইরর থ্রো করার জন্য আমাদেরকে ভিউ থেকে আমাদের একটি `Http404` এক্সেপশন রেইজ করলেই চলবে । যেমন `detail()` ভিউটি হতে পারে এমন: 

	from django.http import Http404

	def detail(request, poll_id):
    	try:
        	poll = Poll.objects.get(pk=poll_id)
    	except Poll.DoesNotExist:
        	raise Http404
    	return render(request, 'polls/detail.html', {'poll': poll})
    	
 এই ভিউটির জন্য টেম্প্লেট আমরা পরে দেখবো তবে টেস্ট করার জন্য `polls/details.html` ফাইলে নিম্নোক্ত কন্টেন্ট যোগ করা যায়: 
 
	{{ poll }}
 
 
 এবার ব্রাউজারে `http://localhost:8000/polls/34` ভিজিট করলে 404 ইরর মেসেজ পাওয়া যাবে । 
 	