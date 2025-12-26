import numpy as np
import networkx as nx

# ===============================
# EBESM GOD MODE CORE
# Enformasyon-Temelli Bilinç Modeli
# ===============================

class EBESMSystem:
    def __init__(self, node_count=120, connectivity=0.08):
        self.node_count = node_count
        self.connectivity = connectivity
        self.graph = self._initialize_network()
        self.state = np.random.rand(node_count)
        self.time = 0

    def _initialize_network(self):
        G = nx.erdos_renyi_graph(self.node_count, self.connectivity)
        for u, v in G.edges():
            G[u][v]['weight'] = np.random.uniform(0.1, 1.0)
        return G

    # -------------------------------
    # CORE INFORMATION DYNAMICS
    # -------------------------------
    def update_state(self):
        new_state = np.zeros(self.node_count)
        for i in range(self.node_count):
            influence = 0
            for j in self.graph.neighbors(i):
                w = self.graph[j][i]['weight']
                influence += w * self.state[j]
            new_state[i] = np.tanh(influence)
        self.state = new_state
        self.time += 1

    # -------------------------------
    # EBESM METRICS
    # -------------------------------
    def information_density(self):
        return np.mean(np.abs(self.state))

    def integration(self):
        return nx.average_clustering(self.graph)

    def self_reference(self):
        corr = np.corrcoef(self.state, np.roll(self.state, 1))[0, 1]
        return 0 if np.isnan(corr) else corr

    def continuity(self, previous_state):
        return np.corrcoef(previous_state, self.state)[0, 1]

    def consciousness_score(self):
        return (
            self.information_density() *
            self.integration() *
            abs(self.self_reference())
        )

    # -------------------------------
    # LIFE / DEATH SIMULATION
    # -------------------------------
    def simulate(self, steps=50):
        history = []
        prev_state = self.state.copy()

        for _ in range(steps):
            self.update_state()
            c = self.continuity(prev_state)
            history.append({
                "time": self.time,
                "info_density": self.information_density(),
                "integration": self.integration(),
                "self_ref": self.self_reference(),
                "continuity": c,
                "consciousness": self.consciousness_score()
            })
            prev_state = self.state.copy()

        return history

    def simulate_death(self, damage_ratio=0.6):
        """Biological collapse simulation"""
        damaged_nodes = np.random.choice(
            self.node_count,
            int(self.node_count * damage_ratio),
            replace=False
        )

        for node in damaged_nodes:
            self.state[node] = 0
            for neighbor in list(self.graph.neighbors(node)):
                self.graph.remove_edge(node, neighbor)

    # -------------------------------
    # INFORMATIONAL AFTERLIFE TEST
    # -------------------------------
    def informational_continuity_test(self, steps=30):
        results = []
        prev = self.state.copy()

        for _ in range(steps):
            self.update_state()
            cont = self.continuity(prev)
            results.append(cont)
            prev = self.state.copy()

        return np.mean([c for c in results if not np.isnan(c)])


# ===============================
# GOD MODE RUN
# ===============================

if __name__ == "__main__":
    ebesm = EBESMSystem()

    print("=== LIFE PHASE ===")
    life_data = ebesm.simulate(steps=40)
    print("Consciousness Score (Last):", life_data[-1]["consciousness"])

    print("\n=== DEATH EVENT ===")
    ebesm.simulate_death(damage_ratio=0.7)

    print("\n=== POST-DEATH INFORMATION TEST ===")
    continuity_score = ebesm.informational_continuity_test(steps=40)

    print("Informational Continuity After Death:", continuity_score)

    if continuity_score > 0.15:
        print(">>> EBESM RESULT: INFORMATIONAL CONTINUITY PERSISTS")
    else:
        print(">>> EBESM RESULT: INFORMATIONAL STRUCTURE COLLAPSED")
