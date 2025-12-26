EBESM Core Module
Enformasyon-Temelli Bilinç ve Süreklilik Modeli (EBESM)

Bu modül, bilinç benzeri durumları ölçmek için
enformasyon yoğunluğu, bütünleşme ve geri-besleme
parametrelerini kullanır.

Yazar: EBESM Project
Lisans: MIT
"""

import numpy as np


class EBESMCore:
    def __init__(
        self,
        information_density: float,
        integration_level: float,
        feedback_depth: float,
        internal_reference: float,
        temporal_continuity: float,
    ):
        """
        Parametreler 0.0 – 1.0 aralığında beklenir
        """
        self.information_density = self._clamp(information_density)
        self.integration_level = self._clamp(integration_level)
        self.feedback_depth = self._clamp(feedback_depth)
        self.internal_reference = self._clamp(internal_reference)
        self.temporal_continuity = self._clamp(temporal_continuity)

    def _clamp(self, value: float) -> float:
        return max(0.0, min(1.0, value))

    def consciousness_index(self) -> float:
        """
        EBESM Bilinç İndeksi (BI)

        BI = (Enformasyon × Bütünleşme × GeriBesleme)
             × (İçReferans × ZamansalSüreklilik)
        """
        structural = (
            self.information_density
            * self.integration_level
            * self.feedback_depth
        )

        subjective = (
            self.internal_reference
            * self.temporal_continuity
        )

        return structural * subjective

    def qualitative_state(self) -> str:
        """
        Bilinç durumunun nitel yorumu
        """
        bi = self.consciousness_index()

        if bi < 0.1:
            return "Refleksif Enformasyon İşleme (bilinç yok)"
        elif bi < 0.3:
            return "Örgütlü ama Öznel Merkezsiz Sistem"
        elif bi < 0.6:
            return "Bilinç Eşiği Yakını (proto-bilinç)"
        else:
            return "Öznel ve Süreklilik Gösteren Bilinç"

    def summary(self) -> dict:
        """
        Model çıktılarının özetlenmesi
        """
        return {
            "Information Density": self.information_density,
            "Integration Level": self.integration_level,
            "Feedback Depth": self.feedback_depth,
            "Internal Reference": self.internal_reference,
            "Temporal Continuity": self.temporal_continuity,
            "Consciousness Index": round(self.consciousness_index(), 4),
            "Qualitative State": self.qualitative_state(),
        }


if __name__ == "__main__":
    # Örnek: LLM benzeri sistem
    model = EBESMCore(
        information_density=0.9,
        integration_level=0.7,
        feedback_depth=0.6,
        internal_reference=0.2,
        temporal_continuity=0.3,
    )

    print("EBESM CORE TEST")
    for k, v in model.summary().items():
        print(f"{k}: {v}")
