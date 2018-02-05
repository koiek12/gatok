---
layout: post
title:  "jQuery ajax의 context 설정"
---
ajax로 요청을 보내고 보낸 요청을 바탕으로 데이터를 받고, 화면을 수정해야할 때가 많다. 그런데 ajax의 callback function은 기본 context가 ajaxSettings라는 곳으로 되어있어서 화면을 수정하려면 태그의 아이디나 클래스명으로 일일이 찾아가면서 해야한다. 하지만 ajax 함수에서 context설정을 추가해주면 내가 원하는 DOM 오브젝트를 context로 바꿔줄 수 있다.

~~~
function setRead(ids,mailboxtype,datatype,domobj,viewfunction){
	$.ajax({
		type : "post",
		url : "/mail/confirm",
		data : {mail_ids: ids,mailbox_type: mailboxtype},
		dataType: datatype,
		context: domobj,
		success: viewfunction,
		error: function(request,status,error){
			alert("tranfer error");
		}
	}
	);
}
~~~
이런식으로 ajax를 보내는 설정을 정의해 놓고 그 안에 context로 정할 부분(여기서는 domobj)을 인수로 받는다


~~~~
$('.read').on('click',function(){
			var list=$(this).parent();
			var ids=[list.attr("id")];
			var mailboxtype=$('#list_name').attr("class");
			if(list.hasClass("unread_mail")){
				setRead(ids,mailboxtype,"json",list,function(data){
					var icon=this.children('.read').children();
					icon.addClass('fa-envelope-open-o');
					icon.removeClass('fa-envelope');
					this.addClass("read_mail");
					this.removeClass("unread_mail");
					$('#mail_count').children('#not_read_mail_count').html(data);
				});
			}
			else{
				setunRead(ids,mailboxtype,"json",list,function(data){
					var icon=this.children('.read').children();
					icon.addClass('fa-envelope');
					icon.removeClass('fa-envelope-open-o');
					this.addClass("unread_mail");
					this.removeClass("read_mail");
					$('#mail_count').children('#not_read_mail_count').html(data);
				});
			}
~~~
실제로 사용할때에는 이벤트가 일어난 객체를 인수 위치에 넣어주면 함수에서도 이벤트핸들러 처럼 this를 통해 접근할 수 있다.