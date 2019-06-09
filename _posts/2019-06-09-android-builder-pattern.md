---
layout: post
title: Builder Pattern 사용하기 (Kotlin/Java)
excerpt : "Android 개발을 하면서 사용하는 Builder Pattern에 대해"
date:   2019-06-09
tags: [android, kotlin, architecture]
comments: true
---

## 💭 서론 (a.k.a. 사담)

---

드디어 내가 다시 '코틀린 개발자'라는 타이틀을 되찾았다 😭 

자바에서 다시 코틀린으로 돌아오기까지 너무 오래 걸렸다... 그래서 잊어버린 게 너무나도 많기 때문에 이걸 올려? 싶은 것도 올릴 것이다. 😤 😤 😤 😤
<br><br><br>

## Builder Pattern이란?

---

참조를 할 책이 없으므로 당당하게 위키 백과를 첨부해 본다.

[빌더 패턴 - 위키백과, 우리 모두의 백과사전](https://ko.wikipedia.org/wiki/%EB%B9%8C%EB%8D%94_%ED%8C%A8%ED%84%B4)
<br><br><br>

## Builder Pattern을 사용하는 이유와 예

---

Builder Pattern은 주로 파라미터에 많은 값을 넘겨줘야 할 경우(가독성이 떨어짐)나 필수가 아닌 값들을 선택해서 초기화 해줘야 할 경우 유용하게 사용한다. 안드로이드 프로그래밍에서 Builder Pattern은 다이얼로그를 구현할 때 많이 사용한다. 예를 들어 팝업 다이얼로그의 형태가 이미지가 들어갈 때가 있고, 텍스트가 볼드 처리가 된 타이틀이 들어갈 때도 있고, 버튼이 양쪽으로 있을 때가 있고 확인 버튼 하나가 있을 때가 있다. 이런 팝업이 있을 때마다 다이얼로그 클래스를 계속 생성한다면 관리하기가 힘들다. 같이 작업하는 다른 개발자가 내가 만들어 둔 다이얼로그를 발굴(?)하지 못 하고 비슷한 다이얼로그 클래스를 또 만들어, 결국 각자 자기가 만든 다이얼로그를 사용하다 나중에서야 쌍둥이 다이얼로그의 존재를 알게 되는 불상사가 일어난다. 그런 출생의 비밀은 일어나지 않는 편이 좋다.

그래서 주로 기본적인 한 다이얼로그가 버튼 형태, 이미지 유무, 텍스트 유무 정도를 다르게 갖는다면 (색상이나 폰트 크기 같은 세부적인 디자인이 통일되어 있다는 가정 하에) 다이얼로그 하나에 Builder Pattern을 적용해 그때 그때 옵션 값을 줘서 커스텀 된 다이얼로그를 생성한다.
<br><br><br>

## Java로 구현한 Builder Pattern

---
{% highlight java %}
    public class SampleJavaDialog extends Dialog {
    
        private Context mContext;
        private String leftBtnText;
        private String rightBtnText;
        private String contentText;
    
        private TextView tv_left_btn;
        private TextView tv_right_btn;
        private TextView tv_content;
    
        public SampleJavaDialog(Context context, Builder builder) {
            super(context);
            mContext = context;
    
            this.contentText = builder.contentText;
            this.rightBtnText = builder.rightBtnText;
            this.leftBtnText = builder.leftBtnText;
        }
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            requestWindowFeature(Window.FEATURE_NO_TITLE);
            setContentView(R.layout.dialog_sample);
            initView();
        }
    
        private void initView() {
            tv_left_btn = (TextView) findViewById(R.id.tv_left_btn);
            tv_right_btn = (TextView) findViewById(R.id.tv_right_btn);
            tv_content = (TextView) findViewById(R.id.tv_content);
    
    
            if (leftBtnText != null) {
                tv_left_btn.setText(leftBtnText);
                tv_left_btn.setOnClickListener(new View.OnClickListener() {
    
                    @Override
                    public void onClick(View v) {
                        Toast.makeText(mContext, leftBtnText, Toast.LENGTH_SHORT).show();
                        dismiss();
                    }
                });
            }
    
            if (rightBtnText != null) {
                tv_right_btn.setText(rightBtnText);
                tv_right_btn.setOnClickListener(new View.OnClickListener() {
    
                    @Override
                    public void onClick(View v) {
                        Toast.makeText(mContext, rightBtnText, Toast.LENGTH_SHORT).show();
                        dismiss();
                    }
                });
            }
    
            if (contentText != null) {
                tv_content.setText(contentText);
            }
        }
    
        public static class Builder {
            private String leftBtnText;
            private String rightBtnText;
            private String contentText;
    
            public Builder setLeftBtnText(String text) {
                leftBtnText = text;
                return this;
            }
    
            public Builder setRightBtnText(String text) {
                rightBtnText = text;
                return this;
            }
    
            public Builder setContentText(String text) {
                contentText = text;
                return this;
            }
    
            public SampleJavaDialog build(Context context) {
                return new SampleJavaDialog(context, this);
            }
        }
    }
   {% endhighlight %}
<br><br><br>

## Kotlin으로 구현한 Builder Pattern

---
{% highlight kotlin %}
    class SampleKotlinDialog(context: Context, private val builder : Builder) : Dialog(context) {
    
        override fun onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)
            requestWindowFeature(Window.FEATURE_NO_TITLE)
            setContentView(R.layout.dialog_sample)
            initView()
        }
    
        private fun initView() {
            builder.leftBtnText?.let {
                tv_left_btn.text = builder.leftBtnText
                tv_left_btn.setOnClickListener {
                    Toast.makeText(context, builder.leftBtnText, Toast.LENGTH_SHORT).show()
                    dismiss()
                }
            }
    
            builder.rightBtnText?.let {
                tv_right_btn.text = builder.rightBtnText
                tv_right_btn.setOnClickListener {
                    Toast.makeText(context, builder.rightBtnText, Toast.LENGTH_SHORT).show()
                    dismiss()
                }
            }
    
            builder.contentText?.let {
                tv_content.text = builder.contentText
                tv_content.setOnClickListener {
                    Toast.makeText(context, builder.contentText, Toast.LENGTH_SHORT).show()
                }
            }
        }
    
        class Builder {
            var leftBtnText : String? = null
            var rightBtnText : String? = null
            var contentText : String? = null
    
            fun leftBtnText(text : String) = apply { this.leftBtnText = text }
            fun rightBtnText(text : String) = apply { this.rightBtnText = text}
            fun contentText(text : String) = apply { this.contentText = text}
    
            fun build(context : Context) = SampleKotlinDialog(context, this)
        }
    }  
    {% endhighlight %}
<br><br><br>
## Sample Code

---

[commelier/android-builder-pattern-example](https://github.com/commelier/android-builder-pattern-example)

위 repository 샘플 프로젝트를 작성했다.
<br><br><br>
## 참조

---

[https://medium.com/google-developers/kotlin-ifying-a-builder-pattern-e5540c91bdbe](https://medium.com/google-developers/kotlin-ifying-a-builder-pattern-e5540c91bdbe)

[Kotlin-ifying a Builder Pattern](https://medium.com/google-developers/kotlin-ifying-a-builder-pattern-e5540c91bdbe)
