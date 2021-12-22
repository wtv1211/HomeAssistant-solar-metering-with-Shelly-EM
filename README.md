# HomeAssistant-solar-metering-with-Shelly-EM
# More reliable template sensors for solar energy metering when using Shelly EM on the inverter.


template:
  - sensor:
        name: "solar_energy"
        unit_of_measurement: "kWh"
        device_class: energy
        state_class: total
        attributes:         
            last_reset: '1970-01-01T00:00:00+00:00'   
        icon: mdi:solar-panel-large
        state: >
          {% set iesire_kwh = states('sensor.shellyem_channel_2_energy') | float %}
          {% set intrare_kwh = states('sensor.shellyem_channel_1_energy') | float %}
          {% if is_number(states('sensor.shellyem_channel_2_energy')) and is_number(states('sensor.shellyem_channel_1_energy')) %}
            {{ (iesire_kwh - intrare_kwh) | round(1) }}
          {% else %}
            None
          {% endif %}

  - sensor:
      - name: "solar_power"
        unit_of_measurement: "W"
        icon: mdi:solar-power
        device_class: power
        state_class: measurement
        state: >
          {% set iesire_w = states('sensor.shellyem_channel_2_power') | float %}
          {% set intrare_w = states('sensor.shellyem_channel_1_power') | float %}
          {% if is_number(states('sensor.shellyem_channel_2_power')) 
              and is_number(states('sensor.shellyem_channel_1_power')) %}
          {{ ((iesire_w - intrare_w) - 1) | round(1) }}
          {% else %}
            None
          {% endif %}

  - sensor:
      - name: "solar_power_negative_is_zero"
        unit_of_measurement: "W"
        icon: mdi:solar-power
        device_class: power
        state_class: measurement
        state: >
          {% if states('sensor.solar_power') | int > 0 %}
            {{ states('sensor.solar_power') }}
          {% else -%}
            0
          {% endif %}
