# Тикеты для голосования (Voting Tickets) 

---

#### 1. Когда началось proof-of-stake голосование?

Proof-of-stake голосование началось[^7969] на блоке 4096.

---

#### 2. В исходном коде значение TicketPoolSize установлено как 8192, но текущий размер тикет-пула больше этого. Почему?

Это преднамеренно и является неотъемлемой частью динамической регулировки стоимости тикетов[^8965]. The `TicketPoolSize` - это размер целевого пула тикетов, и он очень похож на понятие "целевой сложности" для proof-of-work (PoW). Тот факт, что текущий размер пула тикетов больше целевого, является причиной повышения стоимости тикетов, что, в свою очередь, в конечном итоге приведет к покупке меньшего количества тикетов, и постепенно количество тикетов в пуле уменьшится до целевого. Если оно опустится ниже, цена упадет, что простимулирует продажи тикетов и увеличит их количество в пуле до целевого.

---

#### 3. Если мне не повезло, и мой тикет истекает без голосования, теряю ли я комиссию за транзакцию (transaction fee)?

Да[^9806].

---

#### 4. Голосование в основной сети не производилось до блока 4 096, в то время люди покупали тикеты. Означает ли это, что для этих тикетов существует значительный риск истечь раньше 

Нет[^9806], отсчет срока действия не начинается, пока тикет фактически не добыт в блок. Тикеты просто ожидали в пуле памяти момента, когда они смогут быть включены в блоки, начиная с блока 4096.

---

#### 5. Что произойдет, если ни один (или менее 3) выбранных voters не осуществит свою ssgen-транзакцию? 

Голоса всегда определяются текущей верхушкой блока. Майнеры не начинают добычу нового блока, пока, по крайней мере, 3+ выбранных voters не проведут свои ssgen-транзакции. Таким образом, если найденному блоку не удается получить выбранные голоса[^10219], блок просто "теряется" (orphaned) следующим блоком, найденным другим майнером.

Для более конкретного примера предположим, что текущая верхушка цепи находится в блоке 5000. Голосующие, выбранные блоком 5 000, уже подали свои голоса, поэтому майнеры сбиваются с ног в поисках блока 5 001. Наконец, майнер находит решение для блока 5 001 и отправляет его в сеть. Все daemons (и, следовательно, кошельки) увидят, что уже появился 5 001. Тем не менее, майнеры не сразу начинают добычу с 5 001. Вместо этого они продолжают добычу 5000, пока не появятся 3+ голоса для блока 5001. В этот момент все они переключаются и начинают добывать 5 001. Если эти 3+ голоса так и не появились, другой кандидат на блок 5001 будет найден другими майнерами, все еще работающими над блоком 5 000, которые и отправят свое (иное) решение для блока 5001 в сеть. Поскольку каждый из этих новых блоков-кандидатов на 5001 имеет различный хэш, отбираются разные тикеты.

---

#### 6. Что, если тикет, который у меня есть, пропустил голосование? 

Ваш кошелек автоматически аннулирует тикет в блоке после того, как тот пропущен[^13912], поэтому Вы получаете их обратно сразу после того, как их пропустили, в качестве "незрелых" средств (это означает, что пока они будут "созревать" в течение еще 256 блоков, они не могут быть потрачены).

Например:

```no-highlight
dcrctl --wallet getstakeinfo
```

Выход          | Описание
---             |---
`allmempooltix` | Все тикеты (stake tickets) в пуле памяти (ожидающие добычи в блок).
`ownmempooltix` | Ваши тикеты в пуле памяти (ожидающие добычи в блок).

---

#### 7. В чем разница между пропущенным (missed) и аннулированным (revoked) тикетом? 

Пропущенные тикеты - это тикеты, которые были вызваны голосовать, но не сделали этого. После того, как тикет пропущен, он может быть аннулирован[^14763]. Кошелек автоматически аннулирует пропущенные тикеты, которые он контролирует (при условии, что он разблокирован, чтобы он мог подписать транзакцию аннулирования).

Все `rebroadcastmissed` - это приказ кошельку сделать запрос у daemon сети (`dcrd`) снова предоставить ему список пропущенных тикетов, чтобы еще раз проверить, не нужно ли какой-нибудь аннулировать. Как правило, это необязательно, если, конечно, не случилось, что попытка отозвать тикет была выполнена, но не достигла цели, так как кошелек был заблокирован. Другими словами, если Вы заметили, что количество "revoked" не равно количеству "missed", Вам может потребоваться `rebroadcastmissed`. Как уже сказано, такая необходимость возникает редко, так как кошелек автоматически проверяет это при запуске и ищет пропущенные тикеты на каждом блоке.

Продолжение в [Proof-of-Work Mining](/mining/proof-of-work.md)

---

## <i class="fa fa-book"></i> Источники 

[^7969]: Decred Forum, [Post 7,969](https://forum.decred.org/threads/531/#post-7969)
[^8965]: Decred Forum, [Post 8,965](https://forum.decred.org/threads/531/page-2#post-8965)
[^9806]: Decred Forum, [Post 9,806](https://forum.decred.org/threads/180/page-6#post-9806)
[^10219]: Decred Forum, [Post 10,219](https://forum.decred.org/threads/180/page-6#post-10219)
[^13912]: Decred Forum, [Post 13,912](https://forum.decred.org/threads/1271/#post-13912)
[^14763]: Decred Forum, [Post 14,763](https://forum.decred.org/threads/1335/#post-14763)
