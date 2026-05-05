# SO2_loop.slx — модель Simulink

> Файл `.slx` является бинарным архивом Simulink и в репозитории как
> текстовый источник не сохраняется. Соберите модель в Simulink
> R2023b (или более поздней версии) по приведённой ниже схеме
> и сохраните рядом с `params.m`.

## Структурная схема модели

```
                                    +─ Disturbance step
                                    |
   Step ────►(+)────► PID ─────► W_v ─► W_r ─► Transport Delay ─►(+)─► Display
              ▲       (Kp, Ti)                                      |
              │                                                     │
              └────── W_a ◄─────── Transport Delay ◄────────────────┘
                                  (sample line)
```

Блоки и их параметры (значения подгружаются из `params.m`):

| Блок | Имя в `params.m` | Параметр |
|---|---|---|
| Step (уставка) | `ref` | Step time = 0, Final value = 5 |
| Sum1 | — | List of signs `+-` |
| PID Controller | `Kp`, `Ti` | Form: Ideal, P=Kp, I=Kp/Ti, D=0 |
| Transfer Fcn 1 (клапан) | `Kv`, `Tv` | Numerator [Kv], Denominator [Tv 1] |
| Transfer Fcn 2 (реактор) | `Kr`, `Tr` | Numerator [Kr], Denominator [Tr 1] |
| Transport Delay 1 (газоход) | `tau_tr` | Time delay = tau_tr |
| Transport Delay 2 (проба) | `tau_sample` | Time delay = tau_sample |
| Transfer Fcn 3 (анализатор) | `Ta` | Numerator [1], Denominator [Ta 1] |
| Step (возмущение) | — | Step time = 150, Final value = 0.5 |
| Scope | — | 2 сигнала: уставка и реакция |
| To Workspace 1 | — | Variable `t_sim`, `y_sim` |

## Порядок запуска

1. Запустить `params.m` — рассчитываются все коэффициенты, выводятся
   запасы устойчивости и сохраняется `SO2_model_params.mat`.
2. Открыть `SO2_loop.slx`, нажать **Run**.
3. Сохранить графики переходного процесса для пояснительной записки
   и графической части (лист 3).

## Расширения модели (опционально)

- Добавить блок Feed-Forward по составу шихты (см. п. 2.5.5):
  входной сигнал `S_m` (% серы), коэффициент `Kff = 800`, выход
  суммируется с выходом ПИ-регулятора перед клапаном.
- Заменить идеальный ПИ-блок на функциональный блок `PID_E`
  библиотеки ОВЕН Logic — для проверки реализации в ПЛК.
- Добавить нелинейности (насыщение клапана 0…100 %) — блок Saturation.
