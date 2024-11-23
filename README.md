# -Aeon-s-Ascendant-Essence-of-the-Void-
神明重生后的探索和重塑世界,融合了阴阳元素和元素对抗的理念
import random

# ==== 五行属性 ====
class Element:
    def __init__(self, name, nature):
        self.name = name  # 五行（金、木、水、火、土）
        self.nature = nature  # 阴阳

    def counters(self, other):
        """判断是否克制目标属性"""
        elements = ["金", "木", "水", "火", "土"]
        if elements[(elements.index(self.name) + 1) % 5] == other.name:
            return True
        return self.nature != other.nature  # 阴阳相克

    def __repr__(self):
        return f"{self.name} ({self.nature})"


# ==== 天气系统 ====
class WeatherSystem:
    def __init__(self, weather):
        self.weather = weather

    def modify_damage(self, element, base_damage):
        """根据天气调整伤害"""
        weather_effects = {
            "晴天": lambda el: base_damage * 1.2 if el.nature == "阳" else base_damage,
            "阴天": lambda el: base_damage * 1.2 if el.nature == "阴" else base_damage,
            "雪天": lambda el: base_damage * 1.5 if el.nature == "阴" and el.name == "水" else base_damage,
            "炎热": lambda el: base_damage * 1.5 if el.nature == "阳" and el.name == "火" else base_damage,
        }
        return weather_effects.get(self.weather, lambda el: base_damage)(element)


# ==== 角色状态 ====
class Character:
    def __init__(self, name, max_health=1500, max_energy=160):
        self.name = name
        self.health = max_health
        self.energy = max_energy
        self.max_health = max_health
        self.max_energy = max_energy
        self.position = [0, 0, 0]  # 简化角色位置表示

    def move(self, mode):
        """角色移动"""
        energy_costs = {
            "走路": 5,
            "跑步": 10,
            "游泳": 15,
            "飞行": 20,
        }
        if mode in energy_costs and self.energy >= energy_costs[mode]:
            self.energy -= energy_costs[mode]
            self.position[0] += 1  # 简化位移
            return True
        return False

    def recover(self, health=0, energy=0):
        """恢复生命与体力"""
        self.health = min(self.health + health, self.max_health)
        self.energy = min(self.energy + energy, self.max_energy)

    def __repr__(self):
        return f"{self.name} - Health: {self.health}, Energy: {self.energy}"


# ==== 技能树 ====
class SkillTree:
    def __init__(self):
        self.skills = {
            "暴击率": {"level": 0, "max_level": 5, "effect": lambda x: 5 * x},
            "暴击伤害": {"level": 0, "max_level": 5, "effect": lambda x: 10 * x},
            "元素增强": {"level": 0, "max_level": 5, "effect": lambda x: 10 * x},
        }

    def upgrade(self, skill_name):
        if skill_name in self.skills and self.skills[skill_name]["level"] < self.skills[skill_name]["max_level"]:
            self.skills[skill_name]["level"] += 1
            return True
        return False

    def get_effect(self, skill_name):
        if skill_name in self.skills:
            return self.skills[skill_name]["effect"](self.skills[skill_name]["level"])
        return 0


# ==== 战斗系统 ====
class BattleSystem:
    def __init__(self, weather, skill_tree):
        self.weather = WeatherSystem(weather)
        self.skill_tree = skill_tree

    def calculate_damage(self, attacker, defender, base_damage, crit_rate, crit_multiplier):
        """计算最终伤害"""
        damage = base_damage
        if attacker.counters(defender):
            damage *= 1.5  # 属性克制加成
        damage = self.weather.modify_damage(attacker, damage)
        damage += self.skill_tree.get_effect("元素增强")
        if random.random() < crit_rate / 100:
            damage *= 1 + crit_multiplier / 100
        return damage


# ==== 示例运行 ====
if __name__ == "__main__":
    # 初始化天气、技能树和角色
    current_weather = random.choice(["晴天", "阴天", "雪天", "炎热"])
    skill_tree = SkillTree()
    skill_tree.upgrade("暴击率")
    skill_tree.upgrade("暴击伤害")
    print(f"当前天气：{current_weather}")

    player = Character("玩家")
    enemy = Character("敌人")
    fire = Element("火", "阳")
    water = Element("水", "阴")

    battle_system = BattleSystem(current_weather, skill_tree)
    damage = battle_system.calculate_damage(fire, water, 100, 20 + skill_tree.get_effect("暴击率"), 150)
    print(f"最终伤害：{damage:.2f}")

    player.move("跑步")
    print(player)
