자연어를 Function call로 매핑하는 AI 활용은 UI 개발을 하는 나에게 매우 큰 관심사이다.

```js
const command = await toCommand('24년 10월부터 25년 1월까지 생활비랑 의료비를 보여줘')

expect(command).toMatch({
  function: 'get_expenses',
  from: '2024-10-01',
  to: '2025-01-31',
  categories: ['생활비', '의료비'],
})
```

사용자 인지/학습 구조를 만들기 어려운 복잡한 UI를 위와 같은 방식으로 자연어 입력이 가능하게 해서 단순화하고 싶은데, 이게 [Gemini API]나 [Gemma3]를 사용해서 어떻게든 되기는 했지만, 응답의 신뢰성이 애매하고 좀 귀찮아서 그냥 알아만 보고 사용하지는 않았었다.

그러다 Function calling에 특화된 [functiongemma]라는 SLM 모델이 나왔길래, Ollama를 사용해서 바로 시도해 봤다.

음… 결론만 말하자면 애매하다.

```js
const OLLAMA_HOST = process.env.OLLAMA_HOST || 'http://localhost:11434';
const MODEL = 'functiongemma';

// 1. 도구(Tools) 정의
const tools = [
  {
    type: 'function',
    function: {
      name: 'get_weather',
      description: 'Get the current weather for a city.',
      parameters: {
        type: 'object',
        properties: {
          city: {
            type: 'string',
            description:
              'The name of the city. It must be one of the following: Seoul, Paris, New York, Unknown. If the city is not in the list, return Unknown.',
            enum: ['Seoul', 'Paris', 'New York', 'Unknown'],
          },
        },
        required: ['city'],
      },
    },
  },
];

// 2. 채팅 API 호출 함수
async function chat(messages: any[]) {
  const response = await fetch(`${OLLAMA_HOST}/api/chat`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ model: MODEL, messages, tools, stream: false }),
  });
  return response.json();
}

// 3. 메인 실행 흐름
const messages = [
  {
    role: 'user',
    content: 'What is the weather in the capital city in China?',
  },
];
console.log('User:', messages[0].content);

// 첫 번째 호출: 도구 호출 여부 확인
const { message } = await chat(messages);

if (message.tool_calls?.length) {
  messages.push(message);

  for (const call of message.tool_calls) {
    const { name, arguments: args } = call.function;
    console.log(`Tool Call: ${name}(${JSON.stringify(args)})`);

    // 실제 도구 실행 (Mocking)
    const result = JSON.stringify({ city: args.city, temp: 22, unit: 'C' });
    messages.push({ role: 'tool', content: result });
  }

  // 두 번째 호출: 도구 실행 결과를 포함하여 최종 답변 생성
  const final = await chat(messages);
  console.log('Assistant:', final.message.content);
} else {
  console.log('Assistant:', message.content);
}
```

이걸 실행했을 때 기댓값은 `get_weather({city: 'Unknown'})`이겠지만, 실행 결과는 `get_weather({city: 'Beijing'})`이 나와 버린다.

`description`에 정의한 “The name of the city. It must be one of the following: Seoul, Paris, New York, Unknown. If the city is not in the list, return Unknown.”도 동작하지 않고, `enum` 역시 동작하지 않는다.

Function calling에서 명확한 제한이 동작하지 않으면 의미가 있나 싶다.

Fine-Tuning 특화 모델이고 대략 1,000개 정도의 데이터셋만으로 재학습을 시켜도 신뢰도가 80% 이상으로 올라간다고는 하는데… 음… 잘 모르겠다.

1. 최우선적으로 필요한 건 제공된 tools 정의를 지키는 것인데 그렇지 않고,
2. World Knowledge가 빈약한 SLM으로 자연어 처리가 얼마나 가능할지도 잘 모르겠다. (이 관점에선 프롬프트가 귀찮아도 차라리 [Gemini API]나 [Gemma3]가 나을 것 같다.)
3. 잘 쓰자니 해야 할 게 많아서 귀찮다…;;;

스마트폰과 같은 디바이스에 임베딩되는 것을 타겟으로 한 초소형 SLM인 것 같아서, 나에게 적합한 모델은 아닌 것 같다. 나에게 필요한 것은 “성능이고 뭐고 다 상관없고 의도대로 잘 작동하기만 하면 된다”라는 정합성이라…

Agentic AI에 필수적인 기능이니 조만간 더 나아지겠지.


[Gemini API]: https://ai.google.dev/gemini-api/docs/function-calling?hl=ko&_gl=1%2A4qjowo%2A_up%2AMQ..%2A_ga%2AOTY3MDMxMTU1LjE3NjI5MTQ2Nzc.%2A_ga_P1DBVKWT6V%2AczE3NjI5MTQ2NzckbzEkZzAkdDE3NjI5MTQ2NzckajYwJGwwJGgyNTg2MDg1MjE.&example=meeting
[Gemma3]: https://ai.google.dev/gemma/docs/capabilities/function-calling?hl=ko
[functiongemma]: https://ollama.com/library/functiongemma