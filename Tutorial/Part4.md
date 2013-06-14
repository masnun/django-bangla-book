# টিউটোরিয়াল - চতুর্থ অধ্যায় 

তৃতীয় অধ্যায়ে আমরা যেখানে শেষ করেছি সেখান থেকেই আমরা চতুর্থ অধ্যায় শুরু করবো । আগের অধ্যায়গুলোর বিষয়বস্তু চোখ বুলিয়ে নিলে এই অধ্যায়টি আত্মস্থ করতে সুবিধা হবে। 

### জ্যাঙ্গোয় ফর্ম তৈরি করা 

ওয়েব এ্যাপ্লিকেশন ডেভেলপ করতে গেলে আমাদেরকে কম বেশি ফর্ম নিয়ে কাজ করতে হয় । ফর্মের মাধ্যমে ব্যবহারকারী ইনপুট প্রদান করতে পারেন । যাদের HTML এর জ্ঞান আছে তারা ইতোমধ্যে `<form>` এর সাথে পরিচিত । জ্যাঙ্গোয় আমরা কিভাবে ফর্ম ডাটা হ্যান্ডল করতে পারি সেটা দেখবো । আসুন `poll/detail.html` টেম্প্লেটটিতে নিচের মত করে কোড যোগ করে নেই - 

	<h1>{{ poll.question }}</h1>

	{% if error_message %}<p><strong>{{ error_message }}</strong></p>{% endif %}

	<form action="{% url 'polls:vote' poll.id %}" method="post">
	{% csrf_token %}
	{% for choice in poll.choice_set.all %}
    	<input type="radio" name="choice" id="choice{{ forloop.counter }}" value="{{ choice.id }}" />
    	<label for="choice{{ forloop.counter }}">{{ choice.choice_text }}</label><br />
	{% endfor %}
	<input type="submit" value="Vote" />
	</form>
	
এখানে আসলে কি করলাম আমরা? 

* প্রতে্যকটা `choice` এর জন্য আমরা একটি করে রেডিও বাটন বসালাম । যখন ব্যবহারকারী যে কোন অপশন নির্বাচন করে ফর্মটি সাবমিট করবে তখন আমরা `POST` ডাটা হিসেবে পাবো এমন কিছু একটা - `choice=3` । (বিষয়টি আরো বিশদভাবো বোঝার জন্য HTML এবং `<form>` সম্পর্কে ভালো করে জানা প্রয়োজন)

* ফর্ম এর `action` হিসেবে আমরা ব্যবহার করেছি `{% url 'polls:vote' poll.id %}` এবং `method` হিসেবে `post` । `url` একটি টেম্প্লেট ট্যাগ যেটি `polls:vote` এবং `poll.id` এর মান ব্যবহার করে সঠিক url টি আউটপুট দিবে । 

* `forloop.counter` দিয়ে আমরা বুঝতে পারি `for` লুপ এ ঠিক কতোবার লুপ ঘটেছে 

* জ্যাঙ্গো স্বাভাবিকভাবে Cross Site Request Forgery ঠেকানোর চেষ্টা করে । এজন্য প্রতিটি ফর্মে `{% csrf_token %}` থাকা অত্যাবশ্যক । এটি না থাকলে জ্যাঙ্গো ধরে নেয় রিকুয়েস্টটিতে কোন ঘাপলা আছে এবং সে আর এটা প্রসেস করে না । যদিও এটা ডিজএ্যাবল করা যায় কিন্তু এটা ব্যবহার করা উচিৎ । 

আমরা আগের অধ্যায়ে নিচের URLconf টি তৈরি করেছিলাম - 

	url(r'^(?P<poll_id>\d+)/vote/$', views.vote, name='vote'), 

সাথে সাথে আমরা একটি ডামি ভিউও তৈরি করেছিলাম যেটা আসলে তেমন কোন কাজই করতো না । এবার আমরা এই ভিউটিকে পরিবর্তন করে নিবো নিচের মত করে - 

	from django.shortcuts import get_object_or_404, render
	from django.http import HttpResponseRedirect, HttpResponse
	from django.core.urlresolvers import reverse
	from polls.models import Choice, Poll
	# ...
	def vote(request, poll_id):
    	p = get_object_or_404(Poll, pk=poll_id)
    	try:
        	selected_choice = p.choice_set.get(pk=request.POST['choice'])
    	except (KeyError, Choice.DoesNotExist):
        	# Redisplay the poll voting form.
        	return render(request, 'polls/detail.html', {
            	'poll': p,
            	'error_message': "You didn't select a choice.",
        	})
    	else:
        	selected_choice.votes += 1
        	selected_choice.save()
        	# Always return an HttpResponseRedirect after successfully dealing
        	# with POST data. This prevents data from being posted twice if a
        	# user hits the Back button.
        	return HttpResponseRedirect(reverse('polls:results', args=(p.id,)))
        	

এখানে কি করছি আমরা? 

* `request.POST` হচ্ছে পাইথন ডিকশনারী । এখানে সব ডাটা কি-ভ্যালু জোড় হিসেবে থাকে । যেমন: `request.POST['choice']` থেকে আমরা `choice` এর মান পেতে পারি । যদি POST ডাটায় `choice` না থাকে তবে জ্যাঙ্গো `KeyError` রেইজ করবে । একারণেই আমরা try…except ব্লক ব্যবহার করেছি । 


* এখানে আমরা `HttpResponse` না পাঠিয়ে `HttpResponseRedirect` রিটার্ন করছি । এটার মাধ্যমে কোন আউটপুট না দেখিয়ে আমরা ব্যবহারকারীকে অন্য ঠিকানায় পাঠিয়ে দিতে পারি । 

* `reverse()` ফাংশনটি `{% url %}` টেম্প্লেট ট্যাগের মত করেই  ভিউ এর নাম থেকে পূর্ণ URL রিটার্ন করে ।

এখন এ্যাপ্লিকেশনে কেউ ভোট দিলে আমরা `results()` ভিউতে রিডিরেক্ট করে দিবে । আসুন সেই ভিউটি তৈরি করে নেই এবার - 

	def results(request, poll_id):
    	poll = get_object_or_404(Poll, pk=poll_id)
    	return render(request, 'polls/results.html', {'poll': poll})
    	
এবার ভিউটির জন্য টেম্প্লেট তৈরি করে নেই - 

	<h1>{{ poll.question }}</h1>

	<ul>
	{% for choice in poll.choice_set.all %}
    	<li>{{ choice.choice_text }} -- {{ choice.votes }} vote{{ choice.votes|pluralize }}</li>
	{% endfor %}
	</ul>

	<a href="{% url 'polls:detail' poll.id %}">Vote again?</a>
	

এবার এ্যাপ্লিকেশনটি রান করুন এবং ভোট দিয়ে দেখুন কেমন কাজ করে । 


### শেষ কথা

এই ৪টি অধ্যায় ভালো করে রপ্ত করতে পারলে জ্যাঙ্গো ব্যবহার করে সাধারণ ওয়েবসাইট খুব সহজেই তৈরি করতে পারবেন । তবে এখানেই থেমে থাকলে চলবে না । এই টিউটোরিয়ালটি নবীনদের কথা চিন্তা করে অফিশিয়াল টিউটোরিয়াল থেকে বেশ পরিবর্তন পরিবর্ধন করে লেখা হয়েছে । তাই অধিকাংশ সময়েই বেস্ট প্র্যাক্টিসের চাইতে সহজবোধ্যতার উপরে জোর দেওয়া হয়েছে । জ্যাঙ্গোর এ্যাডভান্সড কনসেপ্টগুলো আয়ত্ব করতে পারলে এই কাজগুলো করা যায় আরো সহজে, আরো সুন্দর করে । 

জ্যাঙ্গোর নানা ফিচার, বেস্ট প্র্যাকটিস কিংবা এ্যাডভান্সড অনেক কনসেপ্ট নিয়ে আলাদা আলাদা ভাবে আলোচনা করা হবে এই বইয়ের অন্য অংশে । 