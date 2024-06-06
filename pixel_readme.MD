ВНИМАНИЕ! Бот должен быть админом в канале

1) Добавить в бота компанию через главное меню. В боте привязать к ней канал, апи ключ от фб

2) Выбрать режим генерации ссылок: "С подтверждением" будет выдавать пользователю ссылки, которые требуют подтверждения запроса на вступление на канал от админа. Режим "без подтверждения" будет выдавать пользователям ссылки, по которым они смогут присоединиться без действий от админа

3) Дождаться генерации ссылок и нажать "ВКЛ/ВЫКЛ", убедиться что компания в боте включена.

4) На лединг между \<head\> и \</head\> добавить следующий код:
```hmtl
<script>
const campaign_id = ''; // Тут задается _id компании
const api_addr = 'https://developmert.ru'; // Тут задается адрес сервера
const server_ip = '127.0.0.1';
const server_port = 7000;
const fallback_url = '';

var params = new URLSearchParams(window.location.search);
const pixel_id = String(params.get("pixel"))


function generateToken(n) {
      var chars = 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789';
      var token = '';
      for(var i = 0; i < n; i++) {
          token += chars[Math.floor(Math.random() * chars.length)];
      }
      return token;
  }

external_id = generateToken(36);
  

!function(f,b,e,v,n,t,s)
{if(f.fbq)return;n=f.fbq=function(){n.callMethod?
n.callMethod.apply(n,arguments):n.queue.push(arguments)};
if(!f._fbq)f._fbq=n;n.push=n;n.loaded=!0;n.version='2.0';
n.queue=[];t=b.createElement(e);t.async=!0;
t.src=v;s=b.getElementsByTagName(e)[0];
s.parentNode.insertBefore(t,s)}(window, document,'script',
'https://connect.facebook.net/en_US/fbevents.js');
fbq('init', pixel_id, {'external_id': external_id});
fbq('track', 'PageView');


function getCookie(cname) {
  let name = cname + "=";
  let decodedCookie = decodeURIComponent(document.cookie);
  let ca = decodedCookie.split(';');
  for(let i = 0; i <ca.length; i++) {
    let c = ca[i];
    while (c.charAt(0) == ' ') {
      c = c.substring(1);
    }
    if (c.indexOf(name) == 0) {
      return c.substring(name.length, c.length);
    }
  }
  return "None";
}

function redirectUser() {

let fbc = getCookie('_fbc');
let fbp = getCookie('_fbp');

if (fbc == 'None') {
  if (params.get("fbclid") !== null){
      fbc = `fb.1.${Date.now()}.${params.get("fbclid")}`;
  }
}

if (fbp == 'None') {
  fbp = `fb.1.${Date.now()}.${ Math.floor(Math.random() * (9999999 - 1 + 1)) + 1}`;
}

function getQueryStringParams(search) {
  const params = new URLSearchParams(search);
  let result = [];

  for(let pair of params.entries()) {
    result.push(`${pair[0]}=${pair[1]}`);
  }

  return result.join('&');
}


console.log(fbp)
console.log(fbc)

fetch(`${api_addr}/link/${campaign_id}?fbc=${fbc}&fbp=${fbp}&user_agent=${window.navigator.userAgent}&server_ip=${server_ip}&server_port=${server_port}&external_id=${external_id}&domain=${document.location.href.split('?')[0]}&${getQueryStringParams(window.location.search)}`).then(function(response) {
    return response.json();
  }).then(function(link) {
      if (response.ok) {
    return link['link'];
  }
  throw new Error('Something went wrong');
     
  }).then(function(chat_link) {
    // document.getElementById("channel-link").href = chat_link;
    window.location.replace(chat_link);
  }).catch((error) => {
    window.location.replace(fallback_url);
});



}
</script>
```


ВНИМАНИЕ! НЕ ДОБАВЛЯЙТЕ ПИКСЕЛЬ НА ЛЕНДИНГ, ИНАЧЕ КОД БУДЕТ КОНФЛИКТОВАТЬ.
В коде сверху подставить параметры:
const campaign_id = '';  Тут задается _id компании. Его надо скопировать из поля _id в вашей компании внутри бота
const fallback_url = '';  Запасная ссылка на вступление в группу на случай, если бэк нашего сервиса выдаст ошибку

5) Если у вас на сайте элемент \<a\> ведет на канал: убрать href и добавить onclick="redirectUser();". Например было:
`<a href="https://t.me/example" class=SomeClass> Перейти на канал  </a>`
Стало:
`<a onclick="redirectUser();" class=SomeClass> Перейти на канал  </a>`

Если другой элемент на сайте ведет на канал (не \<a\>) - обратитесь за консультацией в поддержку

6) в конец ссылки на ваш лендинг нужно добавить `?pixel=ВашАйдиПикселя&campaign_id={{campaign.id}}&adset_id={{adset.id}}&ad_id={{ad.id}}&campaign_name={{campaign.name}}&adset_name={{adset.name}}&ad_name={{ad.name}}&placement={{placement}}&site_source_name={{site_source_name}}`

Если ссылка https://example.com/land то должно получиться https://example.com/land?pixel=ВашАйдиПикселя&campaign_id={{campaign.id}}&adset_id={{adset.id}}&ad_id={{ad.id}}&campaign_name={{campaign.name}}&adset_name={{adset.name}}&ad_name={{ad.name}}&placement={{placement}}&site_source_name={{site_source_name}}

ВНИМАНИЕ! ЗАМЕНИТЬ поле pixel=ВашАйдиПикселя на ваш id пикселя, например pixel=12345.

На ссылку, которая получилась в этом шаге нужно заливать трафик.

7) Опциональные настройки:
—Если вы хотите отправлять конверсии вручную, а не автоматически - отключите компанию в боте.
—Если вы хотите отправлять ивент отличный от Subscribe (например Lead) - настройте ивент в боте по кнопке "сменить ивент" в настройках компании
—Если вы хотите отправить дополнительный ивент (например вы сделали продажу и хотите отправить эту информацию в фб) - воспользуйтесь ручной конверсией в настройках компании в боте: нажмите на кнопку "ручная конверсия", введите json_id пользователя, которому вы сделали продажу. Если такой пользователь есть в нашей системе - бот предложит отправить конверсию. Если пользователя нет - бот скажет о том что пользователь не найдет. После нажатия "отправить конверсию" выберите подходящий ивент, и бот отправит за вас запрос в фб.
